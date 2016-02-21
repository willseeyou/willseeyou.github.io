---
layout: post
title: "The Little Redis Book"
date: 2016-01-26 15:24:00 +0800
categories: redis
---

[The Little Redis Book](http://openmymind.net/redis.pdf) by Karl Seguin

* Nothing will help you understand Redis more than actually experiencing it.

* The latest version of Redis: [Get Latest Redis](http://redis.io/download)

~~~shell
wget http://download.redis.io/releases/redis-3.0.6.tar.gz
tar xzf redis-3.0.6.tar.gz
cd redis-3.0.6
make
~~~

* Start Redis Server:  `./redis-server`
* Start Redis Console: `./redis-cli`

* Redis is much more than a simple key-value store

### Redis exposes five data structures
`Strings` `Lists` `Sets` `Hashes` `Sorted sets`

### Keys are how you identify pieces of data
* Using separator is a common approach people use to organize their keys<br>
`users:hongwei`

### Values represent the actual data associated with the key
* Redis treats values as a `byte array` and doesn't care what they are

~~~shell
set users:hongwei '{"name":"hongwei", "planet":"earth","likes":["eating"]}'

get users:hongwei
~~~

### Redis is not suitable for every scenario

### Strings
`strlen` : get the length of a key's value<br>
`getrange` : return the specified range of a value<br>
`append` : append the value to the existing value<br>
`incr` `incrby` `decr` `decrby`<br>
`setbit` `getbit` [Wonderful Post](http://blog.getspool.com/2011/11/29/fast-easy-realtime-metrics-using-redis-bitmaps/)
