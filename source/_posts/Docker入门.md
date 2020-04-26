---
title: Docker 入门
entitle: "docker-introduction"
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 其它
timestamp: 1545227266
comments: true
date: 2018-12-19 21:47:46
tags:
  - Docker
keywords:
description: Docker 的一些基本概念及常用命令
photos:
  - /img/tags/docker.png
---

## 简介

Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。

Docker 将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。有了 Docker，就不用担心环境问题。

Docker 系统有两个程序：Docker 服务端 和 Docker 客户端。

- Docker 服务端是一个服务进程，管理着所有的容器。
- Docker 客户端则扮演着 Docker 服务端的远程控制器，可以用来控制 Docker 的服务端进程。
- 大部分情况下，Docker 服务端和客户端运行在一台机器上。

```bash
# 查看 docker 版本
$ docker version
```

### 虚拟机 VS Linux 容器

Linux 容器与虚拟机享有相似的资源隔离和分配，但是两者还是有很大区别的。

> 虚拟机可以在一种操作系统里面运行另一种操作系统。看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。
> Linux 容器是 Linux 的一种虚拟化技术。它不是模拟一个完整的操作系统，而是对进程进行隔离。

由于 Linux 容器是进程级别的，相比虚拟机有很多优势：

- 启动快 (启动虚拟机就相当于启动操作系统)
- 资源占用少 (虚拟机会独占一部分内存和硬盘空间)
- 体积小

### Docker Image

> An image is a lightweight, stand-alone, executable package that includes everything needed to run a piece of software, including the code, a runtime, libraries, environment variables, and config files.

只读模板，包含一个基本的操作系统。

```bash
# 搜索可用镜像
$ docker search tutorial

# 下载镜像（用户名/镜像名:标签名）默认标签为 lastest
$ docker pull learn/tutorial

# 利用该镜像创建一个容器，执行完成会进入 bash 界面，输入 exit 可退出
$ docker run -it learn/tutorials bash

# 查看所有镜像
$ docker images

# 删除镜像（需先删除使用镜像的容器，使用 -f 参数强制删除(不推荐)）
$ docker rmi 镜像名/镜像id
```

### Docker container

> A container is a runtime instance of an image.

一个轻量级的沙箱，容器是从镜像创建的，应用的运行实例。可以启动、开始、停止和删除，容器彼此间相互隔离。

```bash
# 创建容器
# -i：让容器的标准输入保持打开; -t：让docker分配一个伪终端(pseudo-tty)，并绑定到容器的标准输入上。即允许用户交互
$ docker create -it --name ubuntu-demo ubuntu:16.04

# 启动容器
$ docker start 容器名/容器id

# 查看正在运行的容器列表
$ docker ps

# 查看所有容器列表
$ docker ps -a

# 查看刚刚操作的容器id
$ docker ps -l

# 新建并启动容器 (docker run = docker create + docker start)(-d：后台运行)
$ docker run -it ubuntu:16.04 /bin/bash

# run 命令后可跟在镜像中运行的命令
# 使用 Ubuntu 的 apt-get 命令来安装 ping 程序
# 在执行 apt-get 命令的时候，要带上 -y 参数。如果不指定 -y 参数的话，apt-get 命令会进入交互模式，需要用户输入命令来进行确认，但在 Docker 环境中是无法响应这种交互的。
$ docker run learn/tutorial apt-get install -y ping

# 登录容器
$ docker exec -it 容器名/容器id /bin/bash

# 停止容器
$ docker stop 容器名/容器id

# 停止所有容器
$ docker stop $(docker ps -a -q)

# 删除容器
$ docker rm 容器id（需先停止容器或者使用 -f）

# 删除所有容器
$ docker rm $(docker ps -a -q)

```

### Docker 数据管理

```bash
# copy 本地文件到容器内
$ docker cp ./index.html nginx-demo:/usr/share/nginx/html

# Volume
# 将本地的 ~/docker/nginx-demo 目录挂载到容器内部的 /usr/share/nginx/html
$ docker run --name nginx-demo -v ~/docker/nginx-demo:/usr/share/nginx/html -d nginx
```

### Docker 端口映射

在启动容器的时候，如果不指定对应的参数，在容器外部是无法通过网络来访问容器内的网络应用和服务的。

```bash
# 将本地的 8001 端口映射到容器的 80 端口
$ docker run --name nginx-demo -p 8001:80 -d nginx
$ curl http://localhost:8001/

# 查看容器端口映射情况
$ docker port 容器名/容器id
```

### Docker 其它常用命令

```bash
# 登录
$ docker login -u 用户名 -p 密码

# 发布镜像
$ docker push 镜像名
```
