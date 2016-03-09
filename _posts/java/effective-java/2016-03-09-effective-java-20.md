---
layout: post
title: "Effective Java 第20条：类层次优于标签类"
date: 2016-03-09 10:10:00 +0800
categories: effective-java
---

### 标签类缺点
* 样板代码
* 可读性差
* 内存占用高
* 冗长，容易出错，并且效率低下

```java
// Tagged class - vastly inferior to a class hierarchy!
class Figure {
  enum Shape { RECTANGLE, CIRCLE };

  // Tag field - the shape of this Figure
  final Shape shape;

  // These fields are used only if shape is RECTANGLE
  double length;
  double width;

  // This field is used only if shape is CIRCLE
  double radius;

  // Constructor for circle
  Figure(double radius) {
    shape = Shape.CIRCLE;
    this.radius = radius;
  }

  // Constructor for rectangle
  Figure(double length, double width) {
    shape = Shape.RECTANGLE;
    this.length = length;
    this.width = width;
  }

  double area() {
    switch(shape) {
      case RECTANGLE:
        return length * width;
      case CIRCLE:
        return Math.PI * (radius * radius);
      default:
        throw new AssertionError();
    }
  }
}
```

### 类层次纠正了标签类的所有缺点

```java
// Class hierarchy replacement for a tagged class
abstract class Figure {
  abstract double area();
}

class Circle extends Figure {
  final double radius;
  Circle(double radius) { this.radius = radius; }
  double area() { return Math.PI * (radius * radius); }
}

class Rectangle extends Figure {
  final double length;
  final double width;
  Rectangle(double length, double width) {
    this.length = length;
    this.width = width;
  }
  double area() { return length * width; }
}

class Square extends Rectangle {
  Square(double side) {
    super(side, side);
  }
}
```
