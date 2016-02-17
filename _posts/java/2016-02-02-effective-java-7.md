---
layout: post
title:  "Effective Java 第七条：避免使用终结方法"
date:   2016-02-02 09:45:27 +0800
categories: effective-java
---
* 终结方法（finilizer）通常是：`不可预测的` `危险的` `一般情况下是不必要的`
* 终结方法的缺点：`不保证被及时执行`
* 不应该依赖终结方法来更新重要的持久状态
* 使用终结方法有严重的性能损失
* 显式终结方法：`InputStream` `OutputStream` `java.sql.Connection` close

~~~java
Foo foo = new Foo(...);
try {
  // Do what must be down with foo
  ...
} finally {
  foo.terminate(); //Explicite termination method
}
~~~
