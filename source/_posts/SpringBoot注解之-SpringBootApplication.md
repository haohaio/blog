---
title: Spring Boot 注解之@SpringBootApplication
entitle: ''
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 后端
timestamp: 1544879554
comments: true
date: 2018-12-15 21:12:34
tags:
- Spring
keywords: 
description: '@SpringBootApplication 简介'
photos:
- /img/2018/spring.jpg
---

我们经常可以在 Spring Boot 的启动类代码中见到如下代码：

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

下面我们就来了解一下 @SpringBootApplication 这个注解。我们进入该注解的源码后就会发现，其内部主要是由 @ComponentScan、 @SpringBootConfiguration、 @EnableAutoConfiguration 这三个注解组合。接下来我们就来了解一下这三个注解

## @SpringBootConfiguration

查看源码发现 `@SpringBootConfiguration` 被 `@Configuration` 标注，声明当前类是一个配置类，等价于配置文件。再查看 `@Configuration` 源码发现，`@Configuration` 由 `@Component` 标注，表示将当前类添加到 spring 容器中，表示是一个组件。

### Java 配置

Java 配置是通过 `@Configuration` 和 `@Bean` 来实现的

- `@Configuration`：声明当前类是一个配置类，相当于一个 Spring 配置的 xml 文件
- `@Bean`：注解在方法上，声明当前方法返回的是一个 Bean

> Java 配置和注解配置的主要原则是：全局配置使用 Java 配置 (如数据库的相关配置、MVC 相关配置)，业务 Bean 的配置使用注解配置 (@Service、@Component、@Repository、@Controller)

## @EnableAutoConfiguration

查看源码发现，`@EnableAutoConfiguration` 主要由 `@AutoConfigurationPackage` 和 `@Import({AutoConfigurationImportSelector.class})` 这两个注解构成。

- `@AutoConfigurationPackage` 的作用是将引导类所在包及其子包下面所有的组件添加到 spring 容器中。
- `@Import({AutoConfigurationImportSelector.class})` 的作用是就导入自动配置类，即从 classpath 中搜索所有 `META-INF/spring.factories` 配置文件，然后将其中 `org.springframework.boot.autoconfigure.EnableAutoConfiguration` key 对应的配置项加载到 spring 容器中。

## @ComponentScan

被 `@ComponentScan` 标注的类，会被 spring 容器进行管理。

> 四个注解是等效的，可根据需要选用
Spring 里有四大注解：`@Component`、`@Controller`、`@Service` 和 `@Repository`。

`@ComponentScan` 注解就是用来自动扫描被这些注解标识的类，最终生成 IoC 容器里的 Bean。
