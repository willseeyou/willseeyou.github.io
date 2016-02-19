---
layout: post
title: "JDK源码学习：Boolean"
date: 2016-02-19 16:41:00 +0800
categories: jdk
---
#### 类定义

~~~java
public final class Boolean implements java.io.Serializable,
                                      Comparable<Boolean>
{
  private final boolean value;
}
~~~

#### Boolean对象是不可变的
当满足以下条件时，对象才是可变的：
* 对象创建以后其状态就不能修改
* 对象的所有域都是final类型
* 对象是正确创建的（在对象的创建期间，this引用没有逸出）


#### 两个静态变量
* 不用每次都new一个Boolean
* 节省内存
* 避免创建实例的开销

~~~java
public static final Boolean TRUE = new Boolean(true);
public static final Boolean FALSE = new Boolean(false);

// Good
Boolean b = Boolean.TRUE;

// Bad
Boolean b = new Boolean(true);
~~~

#### 两个构造方法
* 能用静态工厂方法就不要使用new [(Effective Java Item 1: 考虑用静态工厂方法替代构造器)](http://willseeyou.github.io/effective-java/2016/01/25/effective-java-1.html)

~~~java
public Boolean(boolean value) {
  this.value = value;
}

public Boolean(String s) {
  this(parseBoolean(s));
}

// 静态工厂方法
public static Boolean valueOf(boolean b) {
  return (b ? TRUE : FALSE);
}

public static Boolean valueOf(String s) {
  return parseBoolean(s) ? TRUE : FALSE);
}
~~~

#### hashCode
* Why use 1231 and 1237? [StackOverflow: Boolean.hashCode](http://stackoverflow.com/questions/3912303/boolean-hashcode)

~~~java
public static int hashCode(boolean value) {
  return value ? 1231 : 1237;
}
~~~

#### equals
* [Effective Java Item 8: 覆盖equals时请遵守通用约定](http://willseeyou.github.io/effective-java/2016/02/03/effective-java-8.html)

~~~java
public boolean equals(Object obj) {
  if (obj instanceof Boolean) {
    return value == ((Boolean) obj).booleanValue();
  }
  return false;
}
~~~

# References
* [JDK源码分析：java.lang.Boolean](http://www.liaoxuefeng.com/article/0013738760210200c0b2b12da1c470a8d771dc7f218e89d000)
