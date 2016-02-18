---
layout: post
title: "Spring Guides: Building a RESTful Web Service"
date: 2016-02-18 15:27:00 +0800
categories: spring
---
### 构建一个 RESTful web service
* 能够接收 `HTTP GET` 请求：

~~~
http://localhost:8080/greeting
~~~

* 使用`JSON`作为响应，返回问候语句:

~~~
{"id":1,"content":"Hello, World!"}
~~~

* 在URL的query string中可以使用自定义的`name`参数:

~~~
http://localhost:8080/greeting?name=User
~~~

* `name`参数替换默认值World，响应JSON如下:

~~~
{"id":1,"content":"Hello, User!"}
~~~

#### 配置pom的parent:

~~~xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>1.3.2.RELEASE</version>
</parent>
~~~

`spring-boot-starter-parent features`:

* 默认编译级别为`Java 1.6`
* 编码格式为`UTF-8`
* `Dependency Management`，忽略`<version>`标签，继承自spring-boot-denepdencies
* Sensible [resource filtering](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-build-systems.html)
* Sensible plugin configuration
* Sensible resource filtering for `application.properties` and `application.yml`

#### 添加spring-boot-maven-plugin:

~~~xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
~~~

`Spring Boot Maven Plugin features`:
1. 将classpath下的所有jar包打进一个可执行über-jar
2. 搜索 public static void main()并把它标记为runnable classpath
3. 设置默认依赖版本号

#### 创建Greeting POJO类

~~~Java
package com.example;

public class Greeting {

	private final long id;
	private final String content;

	public Greeting(long id, String content) {
		this.id = id;
		this.content = content;
	}

	public long getId() {
		return id;
	}

	public String getContent() {
		return content;
	}

}
~~~

`Spring使用Jackson JSON将POJO类转换为JSON`

#### 创建Controller

~~~java
package com.example;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

	private static final String template = "Hello, %s!";
	private final AtomicLong counter = new AtomicLong();

	@RequestMapping("/greeting")
	public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
		return new Greeting(counter.incrementAndGet(),
				String.format(template, name));
	}
}
~~~

* @RestController: shorthand for @Controller and @ResponseBody rolled together [Reference](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html)
* MappingJackson2HttpMessageConverter: 自动将Greeting实例转换为JSON（Jackson 2 在classpath中）[Reference](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/http/converter/json/MappingJackson2HttpMessageConverter.html)

#### 可执行程序

~~~java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
~~~

* @SpringBootApplication: @Configuration + @EnableAutoConfiguration + @ComponentScan

* mvn spring-boot:run

![spring-boot:run](/images/spring/restful-web-service-spring-boot-run.png)

[Code Demo](https://github.com/willseeyou/spring-rest-service)

[Spring Guides References](http://spring.io/guides/gs/rest-service/)
