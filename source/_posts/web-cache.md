---
title: '浏览器的缓存机制'
entitle: web_cache
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1586416562
comments: true
date: 2020-04-09 15:16:02
tags: Web
keywords:
description:
photos:
- /img/tags/web.jpg
---

浏览器缓存机制简单来说，就是第一次访问页面时，会把页面相关资源缓存下来，在之后访问页面的时候，通过页面的缓存策略判断，是否请求新的资源还是使用已经缓存的资源。接下来我们就来具体了解一下。

### 缓存位置

从缓存位置上来说分为四种，并且各自有优先级，依次查找缓存且都没有命中的时候，才会去请求网络。

- Service Worker
- Memory Cache
- Disk Cache
- Push Cache

### Service Worker

Service Worker 是运行在浏览器背后的独立线程，一般可以用来实现缓存功能。使用 Service Worker的话，传输协议必须为 HTTPS。因为 Service Worker 中涉及到请求拦截，所以必须使用 HTTPS 协议来保障安全。

> Service Worker 的缓存与浏览器其他内建的缓存机制不同，它可以让我们自由控制缓存哪些文件、如何匹配缓存、如何读取缓存，并且缓存是持续性的。

Service Worker 实现缓存功能一般分为三个步骤:

- 首先需要先注册 Service Worker
- 然后监听到 install 事件以后就可以缓存需要的文件
- 那么在下次用户访问的时候就可以通过拦截请求的方式查询是否存在缓存，存在缓存的话就可以直接读取缓存文件，否则就去请求数据。

> 当 Service Worker 没有命中缓存的时候，我们需要去调用 fetch 函数获取数据。也就是说，如果我们没有在 Service Worker 命中缓存的话，会根据缓存查找优先级去查找数据。但是不管我们是从 Memory Cache 中还是从网络请求中获取的数据，浏览器都会显示我们是从 Service Worker 中获取的内容。

### Memory Cache

Memory Cache 也就是内存中的缓存，主要包含的是当前中页面中已经抓取到的资源,例如页面上已经下载的样式、脚本、图片等。读取内存中的数据肯定比磁盘快,内存缓存虽然读取高效，可是缓存持续性很短，会随着进程的释放而释放。

> 一旦我们关闭 Tab 页面，内存中的缓存也就被释放了。

计算机中的内存一定比硬盘容量小得多，操作系统需要精打细算内存的使用，所以能让我们使用的内存必然不多，不会将数据都存放在内存。

> 内存缓存在缓存资源时并不关心返回资源的HTTP缓存头Cache-Control是什么值，同时资源的匹配也并非仅仅是对URL做匹配，还可能会对Content-Type，CORS等其他特征做校验。

### Disk Cache

Disk Cache 也就是存储在硬盘中的缓存，读取速度慢点，但是什么都能存储到磁盘中，比之 Memory Cache 胜在容量和存储时效性上。

在所有浏览器缓存中，Disk Cache 覆盖面基本是最大的。它会根据 HTTP Herder 中的字段判断哪些资源需要缓存，哪些资源可以不请求直接使用，哪些资源已经过期需要重新请求。并且即使在跨站点的情况下，相同地址的资源一旦被硬盘缓存下来，就不会再次去请求数据。绝大部分的缓存都来自 Disk Cache。

浏览器会把哪些文件丢进内存中？哪些丢进硬盘中？

- 对于大文件来说，大概率是不存储在内存中的，反之优先
- 当前系统内存使用率高的话，文件优先存储进硬盘

### Push Cache

Push Cache（推送缓存）是 HTTP/2 中的内容，当以上三种缓存都没有命中时，它才会被使用。它只在会话（Session）中存在，一旦会话结束就被释放，并且缓存时间也很短暂，在Chrome浏览器中只有5分钟左右，同时它也并非严格执行HTTP头中的缓存指令。

### 缓存过程分析

- 浏览器每次发起请求，都会先在浏览器缓存中查找该请求的结果以及缓存标识。
- 浏览器每次拿到返回的请求结果都会将该结果和缓存标识存入浏览器缓存中

### 强缓存

不会向服务器发送请求，直接从缓存中读取资源。

请求返回200的状态码，并且Size显示from disk cache或from memory cache

强缓存可以通过设置两种 HTTP Header 实现：Expires 和 Cache-Control。

#### Expires

缓存过期时间，用来指定资源到期的时间，是服务器端的具体的时间点。

HTTP/1 的产物，受限于本地时间，如果修改了本地时间，可能会造成缓存失效。

`Expires: Wed, 22 Oct 2018 08:41:00 GMT` 表示资源会在 Wed, 22 Oct 2018 08:41:00 GMT 后过期，需要再次请求。

#### Cache-Control

HTTP/1.1 的产物

`Cache-Control:max-age=300`时，表示在这个请求正确返回时间（浏览器也会记录下来）的5分钟内再次加载资源，就会命中强缓存。

- public：所有内容都将被缓存（客户端和代理服务器都可缓存）。
- private：所有内容只有客户端可以缓存（默认值）
- no-cache：客户端缓存内容，是否使用缓存则需要经过协商缓存来验证决定。
- no-store：所有内容都不会被缓存，即不使用强制缓存，也不使用协商缓存
- max-age：max-age=xxx (xxx is numeric)表示缓存内容将在xxx秒后失效

> max-age: 0 和 no-cache 在响应报文中意思相同，但在请求报文中，no-cache 时请求报文不会携带协商缓存标识，max-age:0仍会携带。 

#### Expires & Cache-Control

Expires 是http1.0的产物，Cache-Control是http1.1的产物。两者同时存在的话，Cache-Control 优先级高于 Expires。

### 协商缓存

协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程，主要有以下两种情况

协商缓存生效，返回304和Not Modified

#### Last-Modified 和 If-Modified-Since

浏览器在第一次访问资源时，服务器返回资源的同时，在response header中添加 Last-Modified 的header，值是这个资源在服务器上的最后修改时间，浏览器在下一次加载资源向服务器发送请求时，会将上一次返回的 Last-Modified 值放到 request header 里的 If-Modified-Since 里，服务器只需要比较客户端传来的 If-Modified-Since 跟自己服务器上该资源的最后修改时间对比，就能很好地判断是否使用缓存了。

##### 弊端

- 如果打开缓存文件，即使没有对文件进行修改，但还是会造成 Last-Modified 被修改，服务端不能命中缓存导致发送相同的资源
- 因为 Last-Modified 只能以秒计时，如果在不可感知的时间内修改完成文件，那么服务端会认为资源还是命中缓存了，不会返回正确的资源

#### ETag 和 If-None-Match

ETag 是服务器响应请求时，返回当前资源文件的一个唯一标识(由服务器生成)，只要资源有变化，ETag 就会重新生成。浏览器在下一次加载资源向服务器发送请求时，会将上一次返回的 ETag 值放到 request header 里的 If-None-Match 里，服务器只需要比较客户端传来的 If-None-Match 跟自己服务器上该资源的 ETag 是否一致，就能很好地判断资源相对客户端而言是否被修改过了。

##### 两者之间对比

- 首先在精确度上，ETag 要优于 Last-Modified。（Last-Modified的时间单位是秒，ETag 每次都会改变）
- 性能上，ETag 要逊于 Last-Modified，毕竟 Last-Modified 只需要记录时间，而 ETag 需要服务器通过算法来计算出一个 hash 值。
- 优先级上，服务器校验优先考虑 ETag。

### 缓存策略

#### 频繁变动的资源

对于频繁变动的资源，我们可以设置不使用强缓存 `Cache-Control: no-cache`，然后配合 配合 ETag 的缓存策略。

#### 不常变化的资源

对于不经常变化的资源，我们可以主要使用强缓存的策略，设置一个很长的缓存失效时间 `Cache-Control: max-age=31536000`。而为了解决更新的问题，就需要在文件名(或者路径)中添加 hash， 版本号等动态字符。
