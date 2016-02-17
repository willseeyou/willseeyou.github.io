---
layout: post
title: "Effective Java: 第11条：谨慎地覆盖clone"
date: 2016-02-17 09:36:00 +0800
categories: effective-java
---
### Cloneable接口的作用
* 如果一个类实现了Cloneable，Object的clone方法就返回该对象的逐域拷贝，否则就抛出CloneNotSupportedException

### 永远不要让客户去做类库能够替客户完成的事情

### 对象拷贝的好办法：提供拷贝构造器(copy constructor)或拷贝工厂(copy factory)
* public Yum(Yum yum);
* public static Yum newInstance(Yum yum);
