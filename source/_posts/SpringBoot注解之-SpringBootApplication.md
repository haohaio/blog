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

## @ComponentScan

Spring 里有四大注解：@Component、@Controller、@Service 和 @Repository。可用来定义一个 bean。

@ComponentScan 注解就是用来自动扫描被这些注解标识的类，最终生成 IoC 容器里的 Bean。

> 四个注解是等效的，可根据需要选用

- @Component：组件，没有明确的角色
- @Controller：在展现层使用
- @Service：在业务逻辑层使用
- @Repository：在数据访问层使用

### 注入 Bean 的注解

> 三个注解一般情况下是通用的

- @Autowired：Spring 提供的注解
- @Inject：JSR-330 提供的注解
- @Resource：JSR-250 提供的注解

## @SpringBootConfiguration

这个注解的作用与 `@Configuration` 作用相同，都是用来声明当前类是一个配置类。可以通过 `＠Bean` 注解生成 IoC 容器管理的 bean。

### Java 配置

Java 配置是通过 @Configuration 和 @Bean 来实现的

- @Configuration：声明当前类是一个配置类，相当于一个 Spring 配置的 xml 文件
- @Bean：注解在方法上，声明当前方法返回的是一个 Bean

> Java 配置和注解配置的主要原则是：全局配置使用 Java 配置 (如数据库的相关配置、MVC 相关配置)，业务 Bean 的配置使用注解配置 (@Service、@Component、@Repository、@Controller)

## @EnableAutoConfiguration

@EnableAutoConfiguration 是 Spring Boot 实现自动化配置的核心注解，通过这个注解把 Spring 应用所需的 bean 注入容器中。

## 总结

Spring Boot 是通过注解 @EnableAutoConfiguration 的方式，去查找，过滤，加载所需的 configuration，@ComponentScan 扫描我们自定义的 bean，@SpringBootConfiguration 使得被 @SpringBootApplication 注解的类声明为注解类。

> @SpringBootApplication = @SpringBootConfiguration + @EnableAutoConfiguration + @ComponentScan
