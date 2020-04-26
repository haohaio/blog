---
title: "curl 常用用法"
entitle: curl
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 其它
comments: true
date: 2020-04-24 16:09:52
timestamp:
tags:
  - 工具
keywords:
description:
photos:
  - /img/tags/curl.png
---

curl 是常用的命令行工具，用来请求 Web 服务器。它的名字就是客户端（client）的 URL 工具的意思。

它的功能非常强大，命令行参数多达几十种。如果熟练的话，完全可以取代 Postman 这一类的图形界面工具。

今天来了解一下 curl 的常用用法。

### 不带参数

```bash
# 向 `www.example.com` 发送一个 GET 请求，服务器返回的内容会在命令行输出。
$ curl https://www.example.com
```

### -b

`-b` 参数用来向服务器发送 Cookie。

```bash
# 向服务器发送一个名为 foo，值为 bar 的 Cookie。
$ curl -b 'foo=bar' https://www.example.com

# 发送多个 cookie 时，中间用 ; 隔开
$ curl -b 'foo1=bar;foo2=bar2' https://www.example.com
```

### -d

`-d` 参数用于发送 POST 请求的数据体。

```bash
$ curl -d 'username=emma＆password=123' -X POST https://google.com/login
# 或者
$ curl -d 'username=emma' -d 'password=123' -X POST  https://google.com/login
```

### \-\-data-urlencode

`--data-urlencode` 参数等同于 `-d`，发送 POST 请求的数据体，区别在于会自动将发送的数据进行 URL 编码。

```bash
$ curl --data-urlencode 'comment=hello world' https://google.com/login
```

### -G

`-G` 参数用来构造 URL 的查询字符串。

```bash
$ curl -G -d 'q=kitties' -d 'count=20' https://google.com/search
```

上面命令会发出一个 GET 请求，实际请求的 URL 为 `https://google.com/search?q=kitties&count=20`。如果省略 `-G`，会发出一个 POST 请求。

> 如果数据需要 URL 编码，可以结合 \-\-data-urlencode 参数。

### -H

`-H` 参数添加 HTTP 请求的标头。

```bash
$ curl -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json' https://google.com/login
```

### -i

`-i` 参数打印出服务器回应的 HTTP 标头。

```bash
$ curl -i https://www.example.com
```

上面命令收到服务器回应后，先输出服务器回应的标头，然后空一行，再输出网页的源码。

### -I(\-\-head)

`-I` 参数向服务器发出 HEAD 请求，然会将服务器返回的 HTTP 标头打印出来。

```bash
$ curl -I https://www.example.com
```

### \-\-limit-rate

`--limit-rate` 用来限制 HTTP 请求和回应的带宽，模拟慢网速的环境。

```bash
$ curl --limit-rate 200k https://google.com
```

上面命令将带宽限制在每秒 200K 字节。

### -o

`-o` 参数将服务器的回应保存成文件，等同于 `wget` 命令。

```bash
$ curl -o example.html https://www.example.com
```

上面命令将 `www.example.com` 保存成 `example.html`。

### -O

`-O` 参数将服务器回应保存成文件，并将 URL 的最后部分当作文件名。

```bash
$ curl -O https://www.example.com/foo/bar.html
```

上面命令将服务器回应保存成文件，文件名为 `bar.html`。

### -v

`-v` 参数输出通信的整个过程，用于调试。

```bash
$ curl -v https://www.example.com
```

### -X

`-X` 参数指定 HTTP 请求的方法。

```bash
$ curl -X POST https://www.example.com
```

上面命令对 `https://www.example.com` 发出 POST 请求。

### 参考

[curl 的用法指南](http://www.ruanyifeng.com/blog/2019/09/curl-reference.html)
