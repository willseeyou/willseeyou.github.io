---
layout: post
title:  "Effective Java 第五条：避免创建不必要的对象"
date:   2016-01-29 16:38:27 +0800
categories: effective-java
---
### 重用不可变对象
* String s = new String("stringette"); `bad`  //**Don't do this!**
* String s = "stringette"; `good`

### 对于同时提供了静态工厂方法和构造器的不可变类，通常可以使用静态工厂方法而不是构造器，以避免创建不必要的对象
* Boolean.valueOf(String)优于Boolean(String)

### 重用已知不会被修改的可变对象

~~~java
public class Person {
    private final Date birthDate;

    public boolean isBabyBoomer() {
        Calendar gmtCal =
                Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
        Date boomStart = gmtCal.getTime();
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
        Date boomEnd = gmtCal.getTime();
        return birthDate.compareTo(boomStart) >= 0 &&
                birthDate.compareTo(boomEnd) < 0;
    }
}
~~~

~~~java
public class Person {
    private final Date birthDate;

    private static final Date BOOM_START;
    private static final Date BOOM_END;

    static {
        Calendar gmtCal =
                Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
        BOOM_START = gmtCal.getTime();
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
        BOOM_END = gmtCal.getTime();
    }

    public boolean isBabyBoomer() {
        return birthDate.compareTo(BOOM_START) >= 0 &&
                birthDate.compareTo(BOOM_END) < 0;
    }
}
~~~

### 延迟初始化 `Lazy Initializing`

### 优先使用基本类型而不是自动装箱类型

~~~java
public static void main(String[] args) {
  Long sum = 0L;
  for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += i;
  }
  System.out.println(sum);
}
~~~

### 对象池（Object Pool）：重量级对象 `数据库连接池`
