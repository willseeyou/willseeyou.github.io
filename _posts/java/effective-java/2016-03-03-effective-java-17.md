---
layout: post
title: "Effective Java 第17条：要么为继承而设计，并提供文档说明，要么就禁止继承"
date: 2016-03-03 09:17:00 +0800
categories: effective-java
---

### 专门为继承而设计并且具有良好说明文档的类
* 必须有文档说明可覆盖方法的自用性
* java.util.AbstractCollection
* 格言：好的API文档应该描述一个给定的方法做了什么工作，而不是描述如何做
* 继承破坏了封装性带来的不幸后果（描述清楚有可能未定义的实现细节）
* 对于为了继承而设计的类，唯一的测试方法就是编写子类
* 构造器决不能调用可被覆盖的方法
* 无论是clone还是readObject，都不可以调用可覆盖的方法

~~~java
public class Super {
  // Broken - constructor invokes an overridable method
  public Super() {
    overrideMe();
  }

  public void overrideMe() {
    ...
  }
}

////
public final Class Sub extends Super {
  private finale Date date; // Blank final, set by constructor

  Sub() {
    date = new Date();
  }

  // Overriding method invoked by superclass constructor
  @Override
  public void overrideMe() {
    System.out.println(date);
  }

  public static void main(String[] args) {
    Sub sub = new Sub();
    sub.overrideMe();
  }
}
~~~
