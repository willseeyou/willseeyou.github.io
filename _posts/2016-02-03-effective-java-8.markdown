---
layout: post
title:  "Effective Java 第八条：覆盖equals时请遵守通用约定"
date:   2016-02-03 14:16:00 +0800
categories: effective java
---
Object所有的非final方法都有明确的通用约定（general contract）<br>
`equals` `hashCode` `toString` `clone` `finalize`

### 不覆盖equals时，类的每个实例都只与它自身相等
* 类的每个实例本质上都是唯一的：例如Thread
* 不关心是否提供了“逻辑相等”的测试功能
* 超类已经覆盖了equals，从超类继承过来的行为对于子类也是合适的: `Set->AbstractSet` `List->AbstractList` `Map->AbstractMap`
* 类是私有的或是包级私有的，可以确定它的equals方法永远不会被调用

~~~java
@Override
public boolean equals(Object o) {
  throw new AssertionError(); // Method is never called
}
~~~

### 何时应覆盖Object.equals
* 类具有特有的逻辑相等概念
* 超类没有覆盖equals
* 值类(value class)：逻辑上是否相等
* 枚举类型：每个值最多只存在一个对象，不需要覆盖equals

### 覆盖equals需要遵守的约定
* 自反性(reflexive): x.equals(x) 返回true
* 对称性(symmetric): y.equals(x) 返回true时，x.equals(y)也必须返回true
* 传递性(transitive): x.equals(y)返回true, y.equals(z)返回true，那么x.equals(z)也必须返回true
* 一致性(consistent): 只要对象中所用的信息没有被修改，那么多次调用x.equals(y)会一致性地返回true
* 对于任何非null的引用值x，x.equals(null)必须返回false

`没有哪个类是孤立的 --- John Donne`

~~~java
// Broken - violates symmetry!
public final class CaseInsensitiveString {
  private final String s;

  public CaseInsensitiveString(String s) {
    if (s == null)
      throw new NullPointerException();
    this.s = s;
  }

  // Broken - violates symmetry!
  @Override
  public boolean equals(Object o) {
    if (o instanceof CaseInsensitiveString)
      return s.equalsIgnoreCase((CaseInsensitiveString o).s);
    if (o instanceof String) // One-way interoperability!
      return s.equalsIgnoreCase((String) o);
  }
}
~~~

~~~java
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";
// cis.equals(s)返回true
// s.equals(cis)返回false
~~~

~~~java
@Override
public boolean equals(Object o) {
  return o instanceof CaseInsensitiveString &&
    ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
}
~~~

#### 复合优先于继承
`java.sql.Timestamp扩展了java.util.Date，违反了对称性，不要混合使用Date和Timestamp`

#### 不可变类equals需要满足的限制条件：相等的对象永远相等，不相等的对象永远不相等

#### 无论类是否可变，都不要使equals方法依赖于不可靠的资源

### 实现高质量equals方法的诀窍
1. 使用`==`操作符检查“参数是否为这个对象的引用”
2. 使用`instanceof`操作符检查“参数是否为正确的类型”
3. 把参数转换成正确的类型
4. 对于该类中的每个“关键（significant）”域，检查参数中的域是否与该对象中对应的域相匹配
5. 编写完equals之后问自己：是否是对称的、传递的、一致的

`(field == null ? o.field == null : field.equals(o.field))`<br>
`(field == o.field || (field != null && field.equals(o.field)))`

### 告诫
* 覆盖equals时总要覆盖hashCode
* 不要企图让equals方法过于智能
* 不要将equals声明中的Object对象替换为其他的类型
