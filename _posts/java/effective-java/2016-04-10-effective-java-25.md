---
layout: post
title: "Effective Java 第25条：列表优先于数组"
date: 2016-04-10 23:00:00 +0800
categories: effective-java
---
### 数组与泛型的不同点
1. 数组是协变的(covariant)，泛型是不可变的(invariant)
2. 数组是具体化的(reified)

```java
// Fails at runtime!
Object[] objectArray = new Long[1];
objectArray[0] = "I don't fit in"; // Throws ArrayStoreException

// Won't compile!
List<Object> ol = new ArrayList<Long>(); // Incompatible types
ol.add("I don't fit in");
```

### 创建泛型数组是非法的
`new List<E>[]` `new List<String>[]` `new E[]`
