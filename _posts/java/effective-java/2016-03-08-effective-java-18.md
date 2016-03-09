---
layout: post
title: "Effective Java 第18条：接口优于抽象类"
date: 2016-03-08 13:51:00 +0800
categories: effective-java
---
### 接口和抽象类的区别
* 抽象类允许包含某些方法的实现，接口不允许（Java 1.8包含default方法带实现）
* 为了实现由抽象类定义的类型，类必须成为抽象类的一个子类
* Java只支持单继承
* 抽象is-a, 接口like-a
* 抽象类是对`类`抽象，接口是对`行为`抽象

#### 接口是定义mixin类型的理想选择

#### 接口允许构造非层次结构的类型框架

```java
public interface Singer {
  AudioClip sing(Song s);
}
public interface SongWriter {
  Song compose(boolean hit);
}

public interface SingerSongWriter extends Singer, SongWriter {
  AudioClip strum();
  void actSensitive();
}
```

`组合爆炸`

#### 骨架实现AbstractInterface
* AbstractCollection
* AbstractSet
* AbstractList
* AbstractMap
* 骨架实现类是为了继承目的而设计的
