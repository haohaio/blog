---
title: 'Spring Boot 日志配置'
entitle: spring-boot-logging
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 后端
timestamp: 1548385359
comments: true
date: 2019-01-25 11:02:39
tags:
- Spring
keywords:
description: Spring Boot 日志系统
photos:
- /img/2018/spring.jpg
---

Spring Boot 给我们集成了默认的日志系统，但有时我们需要自定义一些配置，今天我们就来了解一下如何进行自定义配置。

> Spring Boot 默认采用了 slf4j+logback 的组合形式。

### 通过 application.properties 修改默认配置

我们可以在 `application.properties` 文件中设置来修改日志默认配置：

```bash
# 日志级别分为 TRACR < DEBUG < INFO < WARN < ERROR 五个级别，优先级由低到高
# 修改日志的 root 级别（默认日志级别）
logging.level.root=debug
# 调整指定包的日志级别
logging.level.com.haohaio=trace
# 当 logging.file 和 logging.path 同时设置时，logging.path 不生效
# 输入到当前项目根路径下的 spring-boot.log 文件中
logging.file=spring-boot.log
# 输入到当前项目根路径下的 logs 文件夹下的 spring-boot.log 文件中
logging.file=logs/spring-boot.log
# 输入到当前项目根路径下的 logs 文件夹里 spring.log 文件中
logging.path=logs
# 修改控制台日志格式
logging.pattern.console=%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} === %msg%n
# 修改输入到日志文件中日志格式
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss.SSS} >>> [%thread] >>> %-5level >>> %logger{50} >>> %msg%n
```

> 日志输出格式说明： %d 表示输出时间，%thread 表示输出当前线程名，%-5level 表示输出日志级别，左对齐5个字符宽度，%logger{50} 表示输出全类名最长50个字符，超过按照句点分割，%msg 表示日志信息，%n 表示换行符

### 自定义配置

我们可以 `resources` 目录下创建对应日志框架的自定义配置文件，来进行自定义配置。

| 日志系统                | 自定义日志配置文件名                                                               |
| :---------------------- | :--------------------------------------------------------------------------------- |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, or `logback.groovy ` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                                                |
| JDK (Java Util Logging) | `logging.properties`                                                               |

#### 自定义 Logback 日志配置

接下来我们可以对默认的 `Logback` 进行自定义配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
    scan：当此属性设置为 true 时，配置文件如果发生改变，将会被重新加载，默认值为true。
    scanPeriod：设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当 scan 为 true 时，此属性生效。默认的时间间隔为 1 分钟。
    debug：当此属性设置为 true 时，将打印出 logback 内部日志信息，实时查看 logback 运行状态。默认值为 false。
-->
<configuration scan="false" scanPeriod="60 seconds" debug="false">
    <!-- 定义日志的根目录 -->
    <property name="LOG_HOME" value="logs" />
    <!-- 定义日志文件名称 -->
    <property name="appName" value="spring-boot"></property>
    <!-- ch.qos.logback.core.ConsoleAppender 表示控制台输出 -->
    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} >>> [%thread] >>> %-5level >>> %logger{50} >>> %msg%n</pattern>
        </layout>
    </appender>

    <!-- 滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件 -->  
    <appender name="appLogAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 指定日志文件的名称 -->
        <file>${LOG_HOME}/${appName}.log</file>
        <!-- TimeBasedRollingPolicy：最常用的滚动策略，它根据时间来制定滚动策略。-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 滚动时产生的文件的存放位置及文件名称 %d{yyyy-MM-dd}：按天进行日志滚动, %i：当文件大小超过 maxFileSize 时，按照 i 进行文件滚动 -->
            <fileNamePattern>${LOG_HOME}/${appName}-%d{yyyy-MM-dd}-%i.log</fileNamePattern>
            <maxFileSize>10MB</maxFileSize>
            <!-- 只保存最近 30 天的文件 -->
            <MaxHistory>30</MaxHistory>
            <totalSizeCap>1GB</totalSizeCap>
        </rollingPolicy>
        <!-- 日志输出格式 -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [ %thread ] - [ %-5level ] [ %logger{50} : %line ] - %msg%n</pattern>
        </layout>
    </appender>

    <!-- logger 主要用于存放日志对象，也可以定义日志类型、级别，name：表示匹配的 logger 类型前缀，也就是包的前半部分，additivity：false：表示rootLogger 的 appender-ref 无效，true：表示当前 logger 的 appender-ref 和 rootLogger 的 appender-ref 都有效
    -->
    <logger name="com.haohaio" level="trace" />
    <logger name="org.springframework" level="debug" additivity="false"></logger>

    <!-- root 与 logger 是父子关系，没有特别定义则默认为 root。-->
    <root level="info">
        <appender-ref ref="stdout" />
        <appender-ref ref="appLogAppender" />
    </root>
</configuration>
```

#### 使用 Profile 特殊配置

我们还可以使用 Profile 特殊配置，根据不同的环境来使用不同的配置。但我们首先需要将自定义日志配置文件名 `logback.xml` 改为 `logback-spring.xml`。比如：

```xml
<springProfile name="dev">
    <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} === [%thread] === %-5level === %logger{50} === %msg%n</pattern>
</springProfile>
<springProfile name="prod">
    <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} >>> [%thread] >>> %-5level >>> %logger{50} >>> %msg%n</pattern>
</springProfile>
```

### 切换日志框架

我们可以在项目的 `pom.xml` 文件里切换日志框架。比如切换为 `log4j2` 日志框架：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <!-- 先排除 spring-boot-starter-logging -->
  <exclusions>
      <exclusion>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-logging</artifactId>
      </exclusion>
  </exclusions>
</dependency>
<!-- 再使用 log4j2 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

切换完成后我们还可以使用 `log4j2` 对应的 `log4j2.xml` 和 `log4j2-spring.xml` 来进行自定义配置。

### 文档

[Spring Boot Logging](https://docs.spring.io/spring-boot/docs/2.1.2.RELEASE/reference/html/boot-features-logging.html)