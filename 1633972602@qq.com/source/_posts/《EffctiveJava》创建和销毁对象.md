---
title: 《EffctiveJava》创建和销毁对象
categories: EffctiveJava学习
tags: java
date: 2018-07-14 21:34:14
---

# 创建和销毁对象
## 用静态工厂方法代替构造器
相比构造器的**优点**：
-	有名称：可以更明确方法的功能；
-	不必在调用时重复创建实例：使不可变类预先创建实例，或将实例缓存起来，避免重复创建对象，提高性能。
-	返回原返回类型的子类型的对象。
-	在创建参数化类型实例时，代码更简洁
	```
	Map<String, List<String>> map = new HashMap<String, List<String>>();
	// 用静态工厂方法实现
	Map<String, String> map = HashMap.newInstence();
	```
	
<!-- more --> 

相比构造器的**缺点**：
-	类如果不含共有的或者受保护的构造器，将不能被子类化。
-	与其他的静态方法没任何区别。
	静态工厂方法的一些命名习惯
	-- valueOf：类型转换方法
	-- of：更简洁的替代，在EnumSet中使用并流行起来
	-- getInstance：对于Singleton来说，该方法没有参数，只会返回一个实例。
	-- newInstance：与getInstance一样，但newInstance能够确保每个新实例与其他实例不同。
	-- getType：像getInstance一样，返回对象类型
	-- newType：想newInstance一样，返回对象类型

综上：构造器和静态工厂方法各有优缺点，优先使用静态工厂方法。

## 遇到多个构造器参数时要考虑用构建器
> Builder模式：利用多个builder构建多个对象。推荐在多参数且参数大多数都是可选的的情况下使用。

- 优点：易于阅读和编写，比JavaBean更安全。
- 缺点：比重叠构造器冗长。

Builder模式示例：
```
public class NutritionFacts {

    private final int servingSize;

    private final int servings;

    private final int colorings;

    private final int fat;

    private final int sodium;

    private final int carbohydrate;

    public static class Builder {
        // 必须的
        private int servingSize = 0;

        private int servings = 0;

        // 非必须的
        private int colorings = 0;

        private int fat = 0;

        private int sodium = 0;

        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servings = servings;
            this.servingSize = servingSize;
        }

        public Builder calories(int colorings) {
            this.colorings = colorings;
            return this;
        }

        public Builder carbohydrate(int carbohydrate) {
            this.carbohydrate = carbohydrate;
            return this;
        }

        public Builder sodium(int sodium) {
            this.sodium = sodium;
            return this;
        }

        public Builder fat(int fat) {
            this.fat = fat;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        this.servingSize = builder.servingSize;
        this.servings =  builder.servings;
        this.colorings =  builder.colorings;
        this.fat =  builder.fat;
        this.sodium =  builder.sodium;
        this.carbohydrate =  builder.carbohydrate;
    }
}

```
调用：

```
 NutritionFacts nutritionFacts = new NutritionFacts.Builder(1, 2)
 				.calories(3).carbohydrate(4).fat(5).sodium(6)
                .build();
```

## 用私有构造器或枚举类型强化Singleton属性
> Singleton是指仅仅被实例化一次的类。Singleton通常被用来表示那些本质上唯一的系统组件，例如窗口管理器或文件系统。

方式一：
```
public class Elvis {

    private Elvis() {
        // 初始化相关操作
    }

    public static final Elvis INSTANCE = new Elvis();

    public void leaveTheBuilding() {
        // ...
    }
}
```
使用：

```
Elvis.INSTANCE;
```

方式二：

```
public class Elvis {

    private Elvis() {
        // 初始化相关操作
    }
    private static final Elvis INSTANCE = new Elvis();

    public static Elvis getInstance() {
        return INSTANCE;
    }

}

```
使用：
```
Elvis.getInstance();
```
## 通过私有构造器强化不可实例化的能力
一些工具类不希望被实例化，实例化对它没有任何意义。

```
public class UtilityClass {

    /**
     * 私有构造方法，保证在任何情况下都不会被实例化
     */
    private UtilityClass() {
        // 防止在当前类调用构造方法
        throw new AssertionError();
    }

    public static boolean empty(String message) {
        if (message == null || "".equals(message)) {
            return Boolean.TRUE;
        }
        return Boolean.FALSE;
    }
}
```
使用：

```
 UtilityClass.empty("hello Word");
```
## 避免创建不必要的对象

- 正确使用String
反例：

```
String s  = new String("hello");	// hello本来就是字符串
```
正确：

```
String s = "hello";
```

- 自动装箱
优先使用基本类型。

## 消除过期的对象引用
一般而言，只要类是自己管理内存，就应该警惕内存泄漏的问题。

```
public class Stack {
    private Object[] elements;

    private int size = 0;

    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size ++] = e;
    }

    public Object pop() {
        if (size == 0) {

        }
        Object e = elements[--size];
        elements[size] = null;
        return e;
    }

    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size+1);
        }
    }
}

```

## 避免使用终结方法
try finally

