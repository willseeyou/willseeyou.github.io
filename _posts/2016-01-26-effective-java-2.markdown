---
layout: post
title: "Effective Java 第二条：遇到多个构造器参数时要考虑用构建器"
date: 2016-01-26 10:57:00 +0800
categories: effective java
---
### 静态工厂和构造器共同的局限性
`不能很好地扩展到大量的可选参数`<br>
`重叠构造器模式可行，但是当有许多参数的时候，客户端代码会很难写，代码难以阅读`<br>
`Java Bean模式：在构造过程中，Java Bean可能处于不一致状态；阻止了把类变成不可变的可能`

### Builder模式既能保证像重叠构造器的安全性，也能保证像Java Bean的可读性

{% highlight java %}
NutritionsFacts cocaCola = new NutritionsFacts.Builder(240, 8)
      .calories(100).sodium(35).carbohydrate(27).build();
{% endhighlight %}

### Apache Commons lang项目有Builder接口

{% highlight java %}
public interface Builder<T> {
  public T build();
}
{% endhighlight %}

1. The Builder interface is designed to designate a class as a builder object in the Builder design pattern.
2. Builders are capable of creating and configuring objects or results that normally take multiple steps to construct or are very complex to derive.

### Builder模式的不足
`为了创建对象，必须先创建它的构造器，有时会面临性能问题`

### 如果类的构造器或者静态工厂中有多个参数，使用Builder模式
