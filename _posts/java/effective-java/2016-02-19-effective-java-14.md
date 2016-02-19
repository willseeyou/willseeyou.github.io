---
layout: post
title: "Effective Java 第14条：在公有类中使用访问方法而非公有域"
date: 2016-02-19 14:17:00 +0800
categories: effective-java
---
### 退化类(Degenerate classes):没有什么作用，只是用来集中实例域

~~~java
// Degenerate classes like this should not be public!
class Point {
  public double x;
  public double y;
}
~~~

### 用包含私有域和公有访问方法(getter)的类代替

~~~java
// Encapsulation of data by accessor methods and mutators
class Point {
  private double x;
  private double y;

  public Point(double x, double y) {
    this.x = x;
    this.y = y;
  }

  public double getX() {
    return x;
  }

  public double getY() {
    return y;
  }

  public void setX(double x) {
    this.x = x;
  }

  public void setY(double y) {
    this.y = y;
  }
}
~~~

### 如果类可以在它所在的包的外部进行访问，就提供访问方法

### 如果类是包级私有的，或者是私有的嵌套类，直接暴露它的数据与并没有本质的错误
