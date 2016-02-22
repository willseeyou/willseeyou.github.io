---
layout: post
title: "Effective Java 第16条：复合优先于继承"
date: 2016-02-22 10:47:00 +0800
categories: effective-java
---
### 继承打破了封装性
* 子类依赖于超类中特定功能的实现细节
* 继承会导致脆弱性

~~~java
// Broken - Inappropriate use of inheritance!
public class InstrumentedHashSet<E> extends HashSet<E> {
  // The number of attempted element insertions
  private int addCount = 0;

  public InstrumentedHashSet() {
  }

  public InstrumentedHashSet(int initCap, float loadFactor) {
    super(initCap, loadFactor);
  }

  @Override
  public boolean add(E e) {
    addCount++;
    return super.add(e);
  }

  // addAll的实现依赖于add
  @Override
  public boolean addAll(Collection<? extends E> c) {
    addCount += c.size();
    return super.addAll(c);
  }

  public int getAddCount() {
    return addCount;
  }
}
~~~

~~~java
// HashSet addAll实现
public boolean addAll(Collection<? extends E> c) {
  boolean modified = false;
  for (E e : c)
    if (add(e)) // addAll基于add实现
      modified = true;
    return modified;
}
~~~

* 在HashSet内部，addAll的方法基于add方法实现
* overriding: 扩展类时，如果只增加新的方法，而不覆盖现有的方法，也有可能出现问题：子类和超类存在签名相同但返回类型不同的方法

### 不扩展现有类，而是在新类中增加一个私有域，它引用现有类的一个实例
* 转发方法（forwarding method）
* 包装类（wrapper class）
* 装饰者模式（Decorator）

~~~java
// Wrapper class - uses composition in place of inheritance
public class InstrumentedSet<E> extends ForwardingSet<E> {
  private int addCount = 0;

  public InstrumentedSet(Set<E> s) {
    super(s);
  }

  @Override
  public boolean add(E e) {
    addCount++;
    return super.add(e);
  }

  @Override
  public boolean addAll(Collection<? extends E> c) {
    addCount += c.size();
    return super.addAll(c);
  }

  public int getAddCount() {
    return addCount;
  }
}

// Reusable forwarding class
public class ForwardingSet<E> implements Set<E> {
  private final Set<E> s;
  public ForwardingSet(Set<E> s) {
    this.s = s;
  }

  public void clear() {
    s.clear();
  }

  public boolean contains(Object o) {
    return s.contains(o);
  }

  public boolean isEmpty() {
    return s.isEmpty();
  }

  public int size() {
    return s.size();
  }

  public Iterator<E> iterator() {
    return s.iterator();
  }
  ...
}

// Example
Set<Date> s = new InstrumentedSet<Date>(new TreeSet<Date>(cmp));
Set<E> s2 = new InstrumentedSet<E>(new HashSet<E>(capacity));
~~~

### 只有当子类真正是超类的子类型时，才适合用继承
* is-a

### Java平台中违反原则的类
* Stack -> Vector
* Properties -> Hashtable

### 正扩展的类API有无缺陷

[Code Reference](https://github.com/willseeyou/effective-java-examples/tree/master/src/main/java/org/effectivejava/examples/chapter04/item16)
