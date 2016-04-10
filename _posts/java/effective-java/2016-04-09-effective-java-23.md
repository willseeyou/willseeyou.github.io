---
layout: post
title: "Effective Java 第23条：请不要在源代码中使用原生态类型"
date: 2016-04-09 22:22:00 +0800
categories: effective-java
---
### 声明中具有一个或多个参数类型的类或者接口，就是泛型类或者接口

### 出错之后应该尽快发现，最好是在编译时就发现

### 如果使用原生态类型，就失去了泛型在安全性和表述性方面的所有优势

### 无限制通配符类型：Set<?>

![generic-terms](/images/effective-java/generic-term.png)
