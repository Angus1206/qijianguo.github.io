---
title: 《EffctiveJava》对于所有对象都通用的方法
categories: EffctiveJava学习
tags: java
---

# 对于所有对象都通用的方法
## 覆盖equals
* 不覆盖equals方法，每个类只与他本身相等

什么时候应该覆盖equals方法呢？
* 类具有自己的 “逻辑相等”，通常属于“值类（value class）”，例如Integer和Date，必须覆盖equals方法，而且可以用作映射表（map)的键（key），或者集合（set）的元素。
* 有一种“值类”不需要覆盖equals方法，即用实例受控确保“每个值至多只存在一个对象”的类。例如枚举，对于这样的类，逻辑相同与对象相同是一回事，因此Object的equals方法等同于逻辑意义上的equals方法。

<!-- more -->

覆盖equals需要注意哪些约定？
* 自反性（reflexive）：if  x != null ，x.equals(x) == true；
* 对称性（symmetric）：x.equals(y) == y.equals(x)
* 传递性（transitive）：x.equals(y), y.equals(z),  则 x.equals(z)
* 一致性（consistent）：多次调用x.equals(y),结果都一样
* 非空性（Non-Nullity）：对于任何非null的值调用x.equals(null) 结果为false

高质量的equals诀窍：
* 使用==操作符检查“参数是否为这个对象的引用”。如果是，则返回true。
* 使用instanceof检查“参数是否为正确的类型”。如果不是，则返回为false。
* 把参数转成正确的类型。因为转换之前用instanceof测试，确保会成功。
* 对于该类中的关键域，检查参数中的域与该对象中对应的域相匹配。
* 编写完equals，检查是否遵循对称，传递，一致性。

告诫：
* 覆盖equals总要覆盖hashCode（hashCode的通用约定）
* 不要让equals过于智能（复杂）
* 不要将equals声明中的Object对象替换为其他的类型。

```
public boolean equals(Object o) {
	...
}
// 而不是
public boolean equals(MyObject o) {
	...
}
```
## 覆盖equals方法总要覆盖hashCode
覆盖equals方法时不覆盖hashCode，就会违反hashCode的通用约定，从而导致该类无法结合所有基于散列的集合一起正常运作，这样的集合包括HashMap、HashSet和HashTable。

## 始终要覆盖toString
建议所有的子类都覆盖这个方法。

## 考虑重写comparable接口

```
public class Person implements Comparable {

    private String name;

    private int age;

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
    public int compareTo(Object o) {
        if (o instanceof Person) {
            Person o1 = (Person) o;
            return o1.getName().compareTo(name) > 0 ? 1 : (o1.getName().compareTo(name) == 0 ? 0 : -1);
        }
        return 0;
    }

    public static void main(String[] args) {
        List<Person> peoples = new ArrayList<Person>(
                Arrays.asList(
                        new Person("a", 1),
                        new Person("b", 2),
                        new Person("a", 1),
                        new Person("d", 3),
                        new Person("f", 5))
        );
        Collections.sort(peoples);
        peoples.forEach(people -> System.out.println(people));

    }
}

```
输出
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190328150632161.png)

