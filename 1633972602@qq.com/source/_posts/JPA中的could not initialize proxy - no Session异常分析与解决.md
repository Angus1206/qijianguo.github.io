---
title: JPA中的could not initialize proxy - no Session异常分析与解决
categories: Java
tags: java
---

转载自：https://blog.csdn.net/blueheart20/article/details/52912023

引言： JPA是一种非常流行和常用的持久化框架标准，其下可以对接若干种不同的实现，在不同的父子表管理中，经常会碰到no Session的问题，该如何解决呢？
<!-- more -->
## 问题的引出

  在进行基于JPA的单元测试中，我们使用JUnit来进行测试数据库的关联表信息读取，结果得到如下错误信息：
  

```
org.hibernate.LazyInitializationException: failed to lazily initialize a collection of role: com.rain.wx.meal.model.DishCategory.dishes, could not initialize proxy - no Session
	at org.hibernate.collection.internal.AbstractPersistentCollection.throwLazyInitializationException(AbstractPersistentCollection.java:587)
	at org.hibernate.collection.internal.AbstractPersistentCollection.withTemporarySessionIfNeeded(AbstractPersistentCollection.java:204)
	at org.hibernate.collection.internal.AbstractPersistentCollection.initialize(AbstractPersistentCollection.java:566)
	at org.hibernate.collection.internal.AbstractPersistentCollection.read(AbstractPersistentCollection.java:135)
	at org.hibernate.collection.internal.PersistentBag.get(PersistentBag.java:449)
	at com.rain.wx.meal.service.DishServiceTest.testDishes(DishServiceTest.java:86)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.springframework.test.context.junit4.statements.RunBeforeTestMethodCallbacks.evaluate(RunBeforeTestMethodCallbacks.java:75)
	at org.springframework.test.context.junit4.statements.RunAfterTestMethodCallbacks.evaluate(RunAfterTestMethodCallbacks.java:86)
	at org.springframework.test.context.junit4.statements.SpringRepeat.evaluate(SpringRepeat.java:84)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.runChild(SpringJUnit4ClassRunner.java:252)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.runChild(SpringJUnit4ClassRunner.java:94)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.springframework.test.context.junit4.statements.RunBeforeTestClassCallbacks.evaluate(RunBeforeTestClassCallbacks.java:61)
	at org.springframework.test.context.junit4.statements.RunAfterTestClassCallbacks.evaluate(RunAfterTestClassCallbacks.java:70)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.run(SpringJUnit4ClassRunner.java:191)
	at org.eclipse.jdt.internal.junit4.runner.JUnit4TestReference.run(JUnit4TestReference.java:86)
	at org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:38)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:459)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:678)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:382)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:192)
```
  经过分析，其中的关键词是： could not initialize proxy - no Session; 基于JPA的实现来分析，就是在进行数据库访问之时，当前针对数据库的访问与操作session已经关闭且释放了，故提示no Session可用。

## 代码实现分析

    让我们来看看具体的代码吧

```
@Entity
@Table(name="dish_category")
@Data
@EqualsAndHashCode(callSuper=false)
@JsonRootName(value="category") 
public class DishCategory extends BaseEntity {
	private static final long serialVersionUID = -7189824224534351030L;
 
	@Column
	private String name;
	
	@Column
	private String description;
	
	
	@OneToMany
	@JoinColumn(name="category_id",referencedColumnName="id")
	private List<MealDish> dishes;
}
```
 另外一个实体Bean为MealDish, 其代码为：
 

```
@Entity
@Table(name = "dish")
@JsonRootName(value="dish") 
//@Lazy(value=false)
public class MealDish extends BaseEntity {
	private static final long serialVersionUID = -3982356728880195795L;
 
	@Column
	private String name;
 
	@Column
	private float price;
 
	@Column(name = "img_url")
	private String imgUrl;
 
	@Column(name="category_id")
	private long categoryId;
 
	@Column
	private boolean active;
 
	// 销售数量
	@Column
	private int soldCount;
        .......
}
```
其中Repository/Service的代码分别如下：  

```
@Service
public class DishServiceImpl implements DishService { 
       @Transactional
	@Override
	public List<DishCategory> getDishCategory() {
		return this.dishCategoryRepo.findAll();
	}
   ........
 }
```
Repository相关的代码都是空代码，无实际的实现，这里再次忽略。

单元测试的代码内容：

```
 @Test
    public void testDishes() throws JsonProcessingException {
    	ObjectMapper mapper = new ObjectMapper();
    	
    	List<DishCategory> categories = this.dishService.getDishCategory();
    	
    	for (DishCategory category  : categories) {
    	    // log.debug(String.valueOf(category.getDishes().get(0)));	
    	}
    	
    	String jsonStr = mapper.writeValueAsString(categories);
    	
    	log.info(jsonStr);
    }
```
## 问题分析

  基于对Hibernate和JPA的理解，在ORM中，其为了提升性能使用了Lazy加载，就是在使用的时候，才会加载额外的数据，故导致了在使用之时再加载数据之时， session失效的问题出现。所以问题的目标点实现提前加载数据。

## 问题的解决

    尝试1：  在Service方法中新增了@Transactional进行事务添加

    结果1：  无效

   尝试2： 在@OneToMany的方法上，使用@Lazy(false)

    结果2：  无效

    尝试3： 在@OneToMany的参数中使用fetch=FetchType=Eager

    结果3:   问题解决

    尝试4： 在application.properties的配置文件中新增spring.jpa.open-in-view=true

    结果4： 问题解决

   方法3的正确的代码内容：
   

```
@Entity
@Table(name="dish_category")
@Data
@EqualsAndHashCode(callSuper=false)
@JsonRootName(value="category") 
public class DishCategory extends BaseEntity {
	private static final long serialVersionUID = -7189824224534351030L;
 
	@Column
	private String name;
	
	@Column
	private String description;
	
	
	@OneToMany(fetch=FetchType.EAGER)
	@JoinColumn(name="category_id",referencedColumnName="id")
	private List<MealDish> dishes;
}
```
方法4的解释与说明：

   这个设置选项:

spring.jpa.open-in-view=true

  其实是之前的openEntityManagerInViewInterceptor，解决在Spring MVC与JPA之间Session的声明周期问题

## 总结

    核心问题在于解决延迟加载为及时加载，及时加载会消耗一定的资源，将其程序的性能，请注意这个问题。

