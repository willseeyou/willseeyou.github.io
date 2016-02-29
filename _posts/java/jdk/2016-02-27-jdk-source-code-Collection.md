---
layout: post
title: "JDK源码学习：Collection"
date: 2016-02-27 09:37:00 +0800
categories: jdk
---
### 接口定义

~~~java
public interface Collection<E> extends Iterable<E> {

}
~~~

~~~java
public interface Iterable<T> {
  Iterator<T> iterator();

  default forEach(Consumer<? super T> action) {
    ...
  }

  default Spliterator<T> spliterator() {
    ...
  }
}
~~~