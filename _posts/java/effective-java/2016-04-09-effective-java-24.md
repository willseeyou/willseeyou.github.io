---
layout: post
title: "Effective Java 第24条：消除非受检警告"
date: 2016-04-09 22:50:00 +0800
categories: effective-java
---
### 编译器警告
* 非受检强制转化警告(unchecked cast warnings)
* 非受检方法调用警告
* 非受检普通数组创建警告
* 非受检转换警告(unchecked conversion warnings)

### 尽可能消除每一个非受检警告
* SuppressWarnings("unchecked") 使用时需要添加注释，解释为什么这么做是安全的
* SuppressWarnings使用的范围尽可能小
