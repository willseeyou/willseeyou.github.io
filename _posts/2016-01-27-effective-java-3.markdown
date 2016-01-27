---
layout: post
title:  "Effective Java 第三条：用私有构造器或者枚举类型强化Singleton属性"
date:   2016-01-27 09:27:00 +0800
categories: effective java
---
### Singleton指仅仅被实例化一次的类

{% highlight java %}
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() {
    ...
  }

  public void leaveTheBuilding() {
    ...
  }
}
{% endhighlight %}

{% highlight java %}
public class Elvis {
  private static final Elvis INSTANCE = new Elvis();
  private Elvis() {
    ...
  }
  public static Elvis getInstance() {
    return INSTANCE;
  }
  public void leaveTheBuilding() {
    ...
  }
}
{% endhighlight %}

### 单元素枚举类型更加简洁，提供了序列化机制，绝对防止多次实例化
{% highlight java %}
public enum Elvis {
  INSTANCE;
  public void leaveTheBuilding() {
    ...
  }
}
{% endhighlight %}

### 单元素的枚举类型已经成为实现Singleton的最佳方法
