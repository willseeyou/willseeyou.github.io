---
layout: post
title:  "Effective Java 第六条：消除过期的对象引用"
date:   2016-02-01 14:32:00 +0800
categories: effective java
---
### 内存泄漏

{% highlight java %}
public class Stack {
  private Object[] elements;
  private int size = 0;
  private static final int DEFAULT_INITIAL_CAPACITY = 16;

  public Stack() {
    elements = new Object[DEFAULT_INITIAL_CAPACITY];
  }

  public void push(Object e) {
    ensureCapacity();
    elements[size++] = e;
  }

  public Object pop() {
    if (size == 0)
      throw new EmptyStackException();
    return elements[--size];
  }

  private void ensureCapacity() {
    if (elements.length == size) {
      elements = Arrays.copyOf(elements, 2 * size + 1);
    }
  }
}
{% endhighlight %}

`栈内维护对象的过期引用（obsolete reference），不会被GC回收`<br>
`无意识的对象保持（unintentional object retention）`

### 消除内存泄漏

{% highlight java %}
public Object pop() {
  if (size == 0)
    throw new EmptyStackException();
  Object result = elements[--size];
  elements[size] = null; //消除过期引用
  return result;
}
{% endhighlight %}

### 清空对象引用应该是一种例外，而不是一种规范行为
`消除过期引用最好的方法是让包含该引用的变量结束其生命周期`

### 只要类是自己管理内存，就应该警惕内存泄漏问题

### 内存泄漏的另一种来源：缓存
`用WeakHashMap代表缓存`

### 内存泄漏的第三种来源：监听器和其他回调
`确保回调立即被当做垃圾回收的最佳方法是：只保存弱引用（weak reference）`
