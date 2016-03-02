---
layout: post
title: "JDK源码学习：Collection"
date: 2016-02-27 09:37:00 +0800
categories: jdk
---
### 接口定义

~~~java
public interface Collection<E> extends Iterable<E> {
  int size;
  boolean isEmpty();
  boolean contains(Object o);
  Iterator<E> iterator();
  Object[] toArray();
  <T> T[] toArray(T[] a);
  boolean add(E e);
  boolean remove(Object o);
  boolean containsAll(Collection<?> c);
  boolean addAll(Collection<? extends E> c);
  boolean removeAll(Collection<?> c);

  default boolean removeIf(Predicate<? super E> filter) {
    ...
  }

  boolean retainAll(Collection<?> c);
  void clear();
  boolean equals(Object o);
  int hashCode();

  default Spliterator<E> spliterator() {
    ...
  }

  default Stream<E> stream() {
    ...
  }

  default Stream<E> parallelStream() {
    ...
  }
}
~~~

#### 所有实现Collection接口的类必须提供两个标准构造函数
* 无参构造函数
* 有一个Collection参数的构造函数

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

~~~java
public interface Iterator<E> {
  boolean hasNext();
  E next();

  default void remove() {
    ...
  }

  default void forEachRemaining(Consumer<? super E> action) {
    ...
  }
}
~~~
