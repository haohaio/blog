---
title: '使用 Docker 搭建 MySQL 服务'
entitle: docker-mysql
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 其它
timestamp: 1546479956
comments: true
date: 2019-01-03 09:45:56
tags:
- Docker
keywords:
description: 使用 Docker 搭建 MySQL 服务
photos:
- /img/tags/mysql.png
---

最近在学习 Spring Boot 的时候，需要搭建一个 mysql 的服务，我们可以通过 docker 非常方便地搭建一个 mysql 服务。只需一个命令：

```bash
# 默认使用 mysql:@latest 镜像，若未安装会自动下载
$ docker run --name mysql-dev -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -d mysql
```

- –-name：给新创建的容器命名，此处命名为 mysql-dev
- -e：配置信息，此处配置 mysql 的 root 用户的登陆密码
- -p：端口映射，此处映射主机 3306 端口到容器 mysql-dev 的 3306 端口
- -d：成功启动容器后输出容器的 ID

mysql 的服务已经搭好了。

可以通过 docker 连接上 mysql 服务，进行其它一些操作

```bash
# 登录容器
$ docker exec -it mysql-dev bash
# 连接 mysql 服务 (-p后面没有空格。第一次连接时会提示在命令行界面上直接使用密码不安全)
$ mysql -p123456
```

```bash
# 创建一个数据库 db_example
mysql> create database db_example;
# 创建用户
mysql> create user 'springuser'@'%' identified by 'ThePassword';
# 给 springuser 用户 db_example 的所有权限
mysql> grant all on db_example.* to 'springuser'@'%';
```

#### Tips

发现使用可视化工具 (笔者用的 Navicat) 连接 mysql 服务时，会报 `Authentication plugin 'caching_sha2_password' cannot be loaded` 错误。是因为新版本的 mysql 加密方式改了, 而当前可视化工具还不支持。所以我们先通过修改密码将加密方式改为以前的加密方式：

```bash
mysql> ALTER USER 'root' IDENTIFIED WITH mysql_native_password BY '123456';
```