---
layout: post
title: "JDK源码学习：FunctionalInterface"
date: 2016-02-22 14:19:00 +0800
categories: jdk
---
### 接口定义

~~~java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface FunctionalInterface {}
~~~

### 函数式接口
* JSR 335

~~~
A functional interface is an interface that has just one abstract method, and thus
represents a single function contract. (In some cases, this "single" method may take
the form of multiple abstract methods with override-equivalent signatures inherited
from superinterfaces; in this case, the inherited methods logically represent a single
method.)
~~~

* Single Abstract Method Interfaces(SAM Interfaces): 表达逻辑上的单一功能约定

* java.lang.Runnable是函数式接口，只有一个抽象方法

~~~java
@FunctionalInterface
public interface Runnable {
  public abstract void run();
}
~~~
