---
layout: post
title: "Effective Java 第15条：使可变性最小化"
date: 2016-02-21 21:25:00 +0800
categories: effective-java
---
### Java平台类库中的不可变类
`String` `基本类型的包装类` `BigInteger` `BigDecimal`

### 使类不可变需要遵循的五条规则
1. 不要提供任何会修改对象状态的方法（mutator）
2. 保证类不会被扩展
3. 使所有的域都是final的
4. 使所有的域都成为私有的
5. 确保对于任何可变组件的互斥访问

### 不可变对象的优点
* 不可变对象比较简单
* 不可变对象本质上是线程安全的，它们不要求同步
* 不可变对象可以自由地被共享
* 不仅可以共享不可变对象，甚至也可以共享它们的内部信息
* 不可变对象为其他对象提供了大量的构件

### 不可变类真正唯一的缺点是，对于每个不同的值都需要一个单独的对象

### 让不可变类变成final的另一种方法
* 让类的所有构造器都变成私有的或者包级私有的，并且添加公有的静态工厂来替代公有的构造器

~~~java
// Immutable class with static factories instead of constructors
public class Complex {
  private final double re;
  private final double im;

  private Complex(double re, double im) {
    this.re = re;
    this.im = im;
  }

  public static Complex valueOf(double re, double im) {
    return new Complex(re, im);
  }
}
~~~
