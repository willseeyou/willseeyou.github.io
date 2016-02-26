---
layout: post
title: "JDK源码学习：Number"
date: 2016-02-26 13:51:00 +0800
categories: jdk
---
### 类定义

~~~java
public abstract class Number implements java.io.Serializable {
  public abstract int intValue();

  public abstract long longValue();

  public abstract float floatValue();

  public abstract double doubleValue();

  public byte byteValue() {
    return (byte)intValue();
  }

  public short shortValue() {
    return (short)intValue();
  }
}
~~~

* BigDecimal, BigInteger, Byte, Double, Float, Integer, Long, Short的父类
