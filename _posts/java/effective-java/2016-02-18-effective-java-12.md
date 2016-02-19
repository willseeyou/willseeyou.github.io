---
layout: post
title: "Effective Java 第12条：考虑实现Comparable接口"
date: 2016-02-18 09:43:00 +0800
categories: effective-java
---

~~~java
public interface Comparable<T> {
  int compareTo(T t);
}
~~~

`compareTo方法没有在Object中声明，它是Comparable接口中唯一的方法`<br>
`等同性比较` `顺序比较`

~~~java
public class WordList {
  public static void main(String[] args) {
    Set<String> s = new TreeSet<String>();
    Collections.addAll(s, args);
    System.out.println(s);
  }
}
~~~

### Java平台中的所有值类（value classes）都实现了Comparable接口

### compareTo方法通用约定
`将这个对象与指定的对象进行比较。当该对象小于、等于或大于指定对象的时候，分别返回一个负整数、零或者正整数。如果由于指定对象的类型而无法与该对象进行比较，则抛出ClassCastException异常`

* 对称性：实现者必须确保所有的x和y都满足sgn(x.compareTo(y)) == -sgn(y.compareTo(x))。
* 传递性：实现者还必须确保这个比较关系是可传递的：(x.compareTo(y) > 0 && y.compareTo(z) > 0)暗示着x.compareTo(z) > 0。
* 一致性：实现者必须确保x.compareTo(y)==0暗示着所有的z都满足sgn(x.compareTo(z)) == sgn(y.compareTo(z))。
* 强烈建议(x.compareTo(y) == 0) == (x.equals(y))。一般来说，任何实现了Comparable接口的类，若违反了这个条件，都应该明确予以说明。

~~~java
public int compareTo(PhoneNumber pn) {
  // Compare area codes
  if (areaCode < pn.areaCode)
    return -1;
  if (areaCode > pn.areaCode)
    return 1;

  // Area codes are equal, compare prefixes
  if (prefix < pn.prefix)
    return -1;
  if (prefix > pn.prefix)
    return 1;

  // Area codes and prefixes are equal, compare line numbers
  if (lineNumber < pn.lineNumber)
    return -1;
  if (lineNumber > pn.lineNumber)
    return 1;

  return 0; // All fields are equal
}
~~~

~~~java
public int compareTo(PhoneNumber pn) {
  // Compare area codes
  int areaCodeDiff = areaCode - pn.areaCode;
  if (areaCodeDiff != 0)
    return areaCodeDiff;

  // Area codes are equal, compare prefixes
  int prefixDiff = prefix - pn.prefix;
  if (prefixDiff != 0)
    return prefixDiff;

  // Area codes and prefixes are equal, compare line numbers
  return lineNumber - pn.lineNumber;
}
~~~

### 注意整数溢出情况
`如果i是一个很大的正整数（int类型），j是一个很大的负整数（int类型），那么(i - j)将会溢出，返回负值`
