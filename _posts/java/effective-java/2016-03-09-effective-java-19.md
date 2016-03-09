---
layout: post
title: "Effective Java 第19条：接口只用于定义类型"
date: 2016-03-09 10:00:00 +0800
categories: effective-java
---
### 常量接口模式是对接口的不良使用
* 泄露实现细节
* 污染子类
* 反面例子：java.io.ObjectStreamConstants

`静态导入(static import)`
