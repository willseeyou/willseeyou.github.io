---
layout: post
title:  "Effective Java 第一条：考虑用静态工厂方法替代构造器"
date:   2016-01-25 15:48:27 +0800
categories: jekyll update
---
### 获取`Class`实例的常用方法：

* 公有的构造器
* 静态工厂方法 `static factory method`

Boolean简单示例：
{% highlight java %}
public static Boolean valueOf(boolean b) {
  return b ? Boolean.TRUE : Boolean.FALSE;
}
{% endhighlight %}

### 静态工厂方法的优点：

* 静态工厂方法有名称:代码易于阅读
{% highlight java %}
BigInteger(int, int Random)
BigInteger.probablePrime
{% endhighlight %}

* 不必在每次调用的时候都创建一个新对象:避免创建不必要的重复对象
{% highlight java %}
实例受控的类：instance-controlled
单例Singleton模式的实现
{% endhighlight %}

* 返回原返回类型的任何子类型的对象:选择返回对象的类有更大的灵活性
{% highlight java %}
隐藏实现类，使API更简洁
通过接口引用被返回的对象
服务提供者框架
{% endhighlight %}

* 在创建参数化类型实例的时候，使代码变得更加简洁
{% highlight java %}
Map<String, List<String>> m = new HashMap<String, List<String>>();
// Java 1.7及之后的版本编译器已经能推断出类型参数
Map<String, List<String>> m = new HashMap<>();

// 类型推导：type inference
public static <K, V> HashMap<K, V> newInstance() {
  return new HashMap<K,V>();
}

Map<String, List<String>> m = HashMap.newInstance();
{% endhighlight %}

### 静态工厂方法的缺点：
* 类如果不含公有的或者受保护的构造器，就不能被子类化
* 与其他的静态方法实际上没有任何区别(API文档中无法明确表示静态工厂方法)
{% highlight code %}
静态工厂方法的惯用名称:
valueOf
of
getInstance
newInstance
getType
newType
{% endhighlight %}
