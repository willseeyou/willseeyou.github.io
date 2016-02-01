---
layout: post
title:  "Effective Java 第三条：用私有构造器或者枚举类型强化Singleton属性"
date:   2016-01-27 09:27:00 +0800
categories: effective java
---
### Singleton指仅仅被实例化一次的类

```java
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() {
    ...
  }

  public void leaveTheBuilding() {
    ...
  }
}
```

```java
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
```

### 单元素枚举类型更加简洁，提供了序列化机制，绝对防止多次实例化
```java
public enum Elvis {
  INSTANCE;
  public void leaveTheBuilding() {
    ...
  }
}
```

### 单元素的枚举类型已经成为实现Singleton的最佳方法

[Relative Post---Java设计模式之singleton](http://linxiaobai.github.io/2015/01/21/java%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B9%8Bsingleton/)
