---
title: '使用 Hexo 搭建属于你自己的博客'
entitle: 'hexo-blog'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 其它
timestamp: 1552009216
comments: true
date: 2019-03-08 09:40:16
tags:
- Hexo
keywords: 
description: 使用 Hexo 快速搭建个人博客
photos:
- /img/tags/hexo.png
---

## 为什么要搭建个人博客

博客，我个人觉得更多是写给自己看的，主要是把自己学到的东西给记录下来，进行总结，从而让自己有一定的提升。其次也能将自己学来的知识与他人进行分享，帮助他人解决问题，在分享的过程中他人和自己都能获得一定的提升。

至于为什么要个人建站，是因为本人是做 Web 开发的，所以想要定制一个自己喜欢的个人博客（其实就是从网上众多的主题中挑选一个自己顺眼的，自己再小小修改一下），想怎么来就怎么来~~

今天就来介绍一下如何使用 Hexo 来搭建自己的个人博客。

## Hexo

[Hexo](https://hexo.io/zh-cn/) 是一个基于 Node.js, 支持 Markdown 的快速、简洁且高效的博客框架。

### 安装 Hexo

需先安装 [Node.js](https://github.com/creationix/nvm) 和 Git

```bash
npm install -g hexo-cli
```

### 创建博客

执行命令

```bash
# 生成博客目录并安装依赖
$ hexo init blog && cd blog && npm install
```

生成的主要文件目录如下：

```bash
├── /scaffolds/          # 模版文件夹：新建文章时的模板
├── /sources/            # 资源文件夹
│ ├── /_posts/           # 文章文件夹
├── /themes/             # 主题文件夹：Hexo 根据主题来生成静态页面
├── _config.yml          # 网站配置信息
├── package.json         # 应用程序信息
```

> 建议将自己的博客用 Git 管理起来哦~~

### 启动服务

执行命令

```bash
# 会在本地启动一个 http server，用来访问我们的个人博客
$ hexo server (hexo s)
```

启动服务后即可在 [本地](localhost:4000) 访问自己刚刚搭建的博客。

### 写作

执行命令

```bash
# hexo new [layout] <title> 创建一篇新文章，layout 默认为 post（scaffolds/post）
$ hexo n 我的第一篇博客
```

> 推荐一份 [Markdown 语法指南](http://note.youdao.com/iyoudao/?p=2411&vendor=unsilent14)

### 部署

我们可以将我们的个人博客部署在服务器上，并代理到我们自己的域名，但这些都是需要 ￥￥ 的。所以推荐一些免费的服务来用来部署我们的博客，比如 [GitHub](https://github.com/)、[Coding](https://coding.net/) 和 [码云](https://gitee.com/) 等，这些平台提供了免费的 Pages 服务，可以用来部署我们的博客。下面以 Github Pages 为例，讲一下如何进行部署，其它平台的操作也是大同小异。

#### GitHub Pages

> GitHub 提供了 GitHub Pages 的服务，免费提供 `username.github.io` 的域名，来让我们部署一个静态网页。

- 首先我们要创建一个 GitHub 帐号，用户名一定要想好了再创建，因为用户名会关系到我们个人博客的域名哦~~
- 然后创建一个名称为 `username.github.io` 的项目，然后点击项目的配置，就会发现，我们的Pages服务已经OK了,如下图所示

![project.png](https://upload-images.jianshu.io/upload_images/1692994-aa18483b1638de5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![pages.png](https://upload-images.jianshu.io/upload_images/1692994-77ac0154b8739377.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 接下来只需要部署我们的代码就可以了

配置 `_config.yml`

```yml
deploy:
- type: git
  repo: https://github.com/haohaio/haohaio.github.io.git
  branch: master
```

执行命令

```bash
# 安装依赖以通过 Git 进行部署，然后生成静态文件进行部署
$ npm install hexo-deployer-git --save && hexo g && hexo d
```

> 最好配置一下 GitHub 的 SSH key，将 repo 改成 SSH 类型的，这样部署的时候就不需要输入用户名和密码啦~~

然后访问一下 `username.github.io` 就能看到自己搭建的博客啦~~

### 主题

但现在我们搭建的博客看上去还不是那么的好看，功能也很少，我们可以通过更换主题来改变一下我们博客的面貌。Hexo 提供了一份[主题列表](https://hexo.io/themes/), 或者可以去 GitHub 上搜索 `hexo theme` 来选择一个喜欢的主题进行更换。

那我们就以 GitHub 上 Star 最多的 [hexo-theme-next](https://github.com/iissnan/hexo-theme-next) 为例来更换我们的主题。

执行命令

```bash
# 创建主题文件夹
$ mkdir themes/next
# 获取主题资源文件
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

配置 `_config.yml`

```yml
theme: next
```

执行命令

```bash
# 重新部署
$ hexo g && hexo d
```

刷新页面后就可以看到我们已经成功的更换主题了，具体还可以根据 [详细介绍](http://theme-next.iissnan.com/getting-started.html) 来让我们的博客更加美观、强大。

## 结语

坚持！坚持！坚持！一定要把写博客的习惯坚持下去~~