---
layout: post
title: "Design Pattern: 简单工厂模式"
date: 2016-03-15 09:50:00 +0800
categories: design-pattern
---
### 简单工厂意图
`提供一个创建对象实例的功能，而无需关心其具体实现。被创建实例的类型可以是接口，抽象类，也可以是具体的类`

### 当看到`new`，就会想到`具体`
* 要针对接口编程，不要针对实现编程，但是在Java中通常使用new创建对象，每次使用new时不正是对实现编程吗？

```java
Duck duck = new MallardDuck();
```
* Duck是接口，所以具有弹性，但MallardDuck是具体的类，new MallardDuck()还是建立了具体类的实例

```java
Duck duck;
if (picnic) {
  duck = new MallardDuck();
} else if (hunting) {
  duck = new DecoyDuck();
} else if (inBathTub) {
  duck = new RubberDuck();
}
```
* 一旦Duck对象有了变化或者扩展，上述代码必须做出修改

### `New`有什么不对劲？
* 针对接口编程，那么通过多态，任何新类都可以实现该接口。不要在代码中使用大量具体类
* `依赖倒置原则DIP(The Dependence Inversion Principle)`：高层模块不应该依赖于低层模块，二者都应该依赖于抽象；抽象不应该依赖于细节，细节应该依赖于抽象
* `开放封闭原则OCP(The Open-Closed Principle)`：类应该对扩展开放，对修改关闭
* 找出变化的方面，把它们从不变的部分分离出来

### 识别变化的方面

```java
Pizza OrderPizza(String type) {
  // 理想情况下，希望Pizza pizza = new Pizza();
  // 但是抽象类或接口无法直接实例化
  Pizza pizza = null;
  if (type.equals("cheese")) {
    pizza = new CheesePizza();
  } else if (type.equals("greek")) {
    pizza = new GreekPizza();
  } else if (type.equals("pepperoni")) {
    pizza = new PepperoniPizza();
  }
  pizza.prepare();
  pizza.bake();
  pizza.cut();
  pizza.box();
  return pizza;
}
```
* 从上面的代码可以清楚找到变化的方面：订购各种不同的pizza。如果想加入新的Pizza类型，或修改旧的Pizza类型时，都需要修改上述代码
* OrderPizza无法对修改关闭，创建对象的代码是发生变化的

### 封装创建对象的代码
* 将创建对象的代码抽取出来，生成一个新的对象，把该鑫对象成为工厂
* 工厂处理创建对象的细节，应用中其它的地方均看不到创建对象的细节

### 定义简单工厂
* PizzaStore通过SimplePizzaFactory取得Pizza实例
* SimplePizzaFactory是应用中唯一用到具体Pizza类的地方
* Pizza可以是抽象类，也可以是接口
* 简单工厂模式不满足OCP，虽然如果对系统进行扩展的话可以增加新的Pizza类型，但是还是会修改工厂类

`在设计模式中，所谓的实现一个接口并不一定表示写一个类，并利用implements关键词来实现某个Java接口。实现一个接口泛指实现某个超类型（可以是类或接口）的某个方法`
