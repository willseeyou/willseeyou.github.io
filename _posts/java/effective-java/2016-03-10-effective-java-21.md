---
layout: post
title: "Effective Java 第21条：用函数对象表示策略"
date: 2016-03-10 09:28:00 +0800
categories: effective-java
---
### 具体策略类

```java
class StringLengthComparator {
  private StringLengthComparator() {}
  public static StringLengthComparator instance = new StringLengthComparator();
  public int compare(String s1, String s2) {
    return s1.length() - s2.length();
  }
}
```

#### 缺点
* 客户端无法传递任何其他的策略

```java
// Strategy interface
public interface Comparator<T> {
  public int compare(T t1, T t2);
}

class StringLengthComparator implements Comparator {
  ...
}
```

#### 具体的策略类往往使用匿名类声明

```java
Arrays.sort(StringArray, new Comparator<String>() {
  public int comparet(String s1, String s2) {
    return s1.length() - s2.length();
  }
});
```

#### 使用匿名类每次调用都会新建一个实例，需要将函数存储到一个私有静态final域中

```java
// Exporting a concrete strategy
class Host {
  private static class StrLenCmp implements Comparator<String>, Serializable {
    public int compare(String s1, String s2) {
      return s1.length() - s2.length();
    }
  }

  public static final Comparator<String> STRING_LENGTH_COMPARATOR = new StrLenCmp();
  ...
}
```
