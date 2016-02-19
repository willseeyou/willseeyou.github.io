---
layout: post
title: "Effective Java 第13条：使类和成员的可访问性最小化"
date: 2016-02-19 10:12:00 +0800
categories: effective-java
---
### 设计良好的模块
* 隐藏所有的实现细节，把它的API和它的实现清晰地隔离开来
* 模块之间只通过API进行通信，一个模块不需要知道其他模块的内部工作情况
* 软件设计基本原则：`信息隐藏` `封装`
* 解耦

### 信息隐藏规则
* 尽可能地使每个类或者成员不被外界访问
* 实例域决不能是公有的 `包含公有可变域的类并不是线程安全的`
* 类具有公有的静态final数组域，或者返回这种域的访问方法，这几乎总是`错误`的

~~~java
// Potential security hole!
public static final Thing[] VALUES = { ... };
~~~

~~~java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> VALUES =
  Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
~~~

~~~java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final Thing[] values() {
  return PRIVATE_VALUES.clone();
}
~~~
