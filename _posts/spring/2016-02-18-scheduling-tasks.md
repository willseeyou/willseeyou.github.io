---
layout: post
title: "Spring Guides: Scheduling Tasks"
date: 2016-02-18 20:49:00 +0800
categories: spring
---
### Scheduling Tasks
* 目的：使用Spring `@Scheduled` 每五秒钟打印当前时间

~~~java
package com.example;

import java.text.SimpleDateFormat;
import java.util.Date;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class ScheduledTasks {

	private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

	@Scheduled(fixedRate = 5000)
	public void reportCurrentTime() {
		System.out.println("The time is now " + dateFormat.format(new Date()));
	}
}
~~~

* fixedRate
* fixedDelay
* @Schedule(cron = "...") [Reference](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/support/CronSequenceGenerator.html)

~~~java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling
public class Application {

	public static void main(String[] args) throws Exception {
		SpringApplication.run(Application.class);
	}
}
~~~

* @EnableScheduling: 确保background task executor创建

[Code Demo](https://github.com/willseeyou/spring-scheduling-tasks)

[Spring Guides References](http://spring.io/guides/gs/scheduling-tasks/)
