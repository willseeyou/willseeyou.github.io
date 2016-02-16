---
layout: post
title: "Effective Java: 第九条：覆盖equals时总要覆盖hashCode"
date: 2016-02-16 09:34:00 +0800
categories: effective java
---
### 在每个覆盖了equals方法的类中，也必须覆盖hashCode方法
`HashMap` `HashSet` `Hashtable`

### hashCode约定
* 在应用程序的执行期间，只要对象的equals方法的比较操作所用到的信息没有被修改，那么对这同一个对象调用多次，hashCode方法都必须始终如一地返回同一个整数。在同一个应用程序的多次执行过程中，每次执行所返回的整数可以不一致。
* 如果两个对象根据equals(Object)方法比较是相等的，那么调用这两个对象中任意一个对象的hashCode方法都必须产生同样的整数结果。
* 如果两个对象根据equals(Object)方法比较是不相等的，那么调用这两个对象中任意一个对象的hashCode方法，则不一定要产生不同的整数结果。但是程序员应该知道，给不相等的对象产生截然不同的整数结果，有可能提高散列表（hash table)的性能。

~~~java
public final class PhoneNumber {
  private final short areaCode;
  private final short prefix;
  private final short lineNumber;

  public PhoneNumber(int areaCode, int prefix, int lineNumber) {
    rangeCheck(areaCode, 999, "area code");
    rangeCheck(prefix, 999, "prefix");
    rangeCheck(lineNumber, 9999, "line number");
    this.areaCode = (short) areaCode;
    this.prefix = (short) prefix;
    this.lineNumber = (short) lineNumber;
  }

  private static void rangeCheck(int arg, int max, String name) {
    if (arg < 0 || arg > max)
      throw new IllegalArgumentException(name + ":" + arg);
  }

  @Override
  public boolean equals(Object o) {
    if (o == this)
      return true;
      if (!(o instanceof PhoneNumber))
        return false;
      PhoneNumber pn = (PhoneNumber) o;
      return pn.lineNumber == lineNumber
        && pn.prefix == prefix
        && pn.areaCode == areaCode;
  }

  //Broken - no hashCode method!
}
~~~

~~~java
Map<PhoneNumber, String> m = new HashMap<PhoneNumber, String>();
m.put(new PhoneNumber(707, 867, 5309), "Jenny");

// m.get(new PhoneNumber(408, 867, 5309))返回Null，不会返回"Jenny"
// 由于PhoneNumber没有覆盖hashCode方法，导致两个相等的实例具有不相等的散列码
~~~

~~~java
// The worst possible legal hash function - never use!
@Override
public int hashCode() {
  return 42;
}
// 散列表退化为链表
~~~

### 好的散列函数：为不相等的对象产生不相等的散列码
~~~
1. 把某个非零的常数值，比如17，保存在一个名为result的int型变量中。
2. 对于对象中的每个关键域f(指equals方法中涉及的每个域)，完成以下步骤：
   a. 为该域计算int类型的散列码c：
     i.   如果该域是boolean类型，则计算(f ? 1 : 0)
     ii.  如果该域是byte、char、short或int类型，则计算(int) f
     iii. 如果该域是long类型，则计算(int) (f ^ (f >>> 32))
     iv.  如果该域是float类型，则计算Float.floatToIntBits(f)
     v.   如果该域是double类型，则计算Double.DoubleToLongBits(f)，然后按照步骤2.a.iii，为得到的long类型值计算散列值
     vi.  如果该域是一个对象引用，并且该类的equals方法通过递归地调用equals的方式来比较这个域，则同样为这个域递归地调用hashCode。如果需要更复杂的比较，则为这个域计算一个“范式(canonical representation)”，然后针对这个范式调用hashCode。如果这个域的值为null，则返回0
     vii. 如果该域是一个数组，则要把每一个元素当作单独的域来处理。也就是说，递归地应用上述规则，对每个重要的元素计算一个散列码，然后根据步骤2.b中的做法把这些散列值组合起来。如果数组域中的每个元素都很重要，可以利用发行版本1.5中增加的其中一个Arrays.hashCode方法
   b. 按照下面的公式，把步骤2.a中计算得到的散列码c合并到result中：
   result = 31 * result + c;
3. 返回result。
4. 写完了hashCoed方法之后，问问自己“相等的实例是否都具有相等的散列码”。要编写单元测试来验证你的推断。如果相等的实例有着不相等的散列码，则要找出原因，并修正错误。
~~~

~~~java
@Override
public int hashCode() {
  int result = 17;
  result = 31 * result + areaCode;
  result = 31 * result + prefix;
  result = 31 * result + lineNumber;
  return result;
}
~~~

### 延迟初始化散列码(lazily initialize)

~~~java
// Lazily initialized, cached hashCode
private volatile int hashCode;

@Override
public int hashCode() {
  int result = hashCode;

  if (result == 0) {
    result = 17;
    result = 31 * result + areaCode;
    result = 31 * result + prefix;
    result = 31 * result + lineNumber;
    hashCode = result;
  }
  return result;
}
~~~
