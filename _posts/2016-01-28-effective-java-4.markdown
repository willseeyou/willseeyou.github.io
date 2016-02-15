---
layout: post
title:  "Effective Java 第四条：通过私有构造器强化不可实例化的能力"
date:   2016-01-28 10:09:00 +0800
categories: effective java
---
`java.lang.Math` `java.util.Arrays` `java.util.Collections`这类工具类实例化没有任何意义

企图将类做成抽象类来强制该类不可实例化是行不通的：子类化

~~~java
// Noninstantiable utility class
public class UtilityClass {
  // Suppress default constructor for noninstantiability
  private UtilityClass() {
    throw new AssertionError();
  }
}
~~~
