---
title: "使用 Sentry 对前端项目进行异常监控"
entitle: sentry
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1587526569
comments: true
date: 2020-04-22 11:36:09
tags:
  - Web
keywords:
description:
photos:
  - /img/tags/web.jpg
---

前面在 [前端异常监控](/front-end/exception-monitor-1586335615.html) 一文里大概了解了前端是如何进行异常监控的，但是要搭建一个完整的异常监控系统还是很麻烦的，不仅仅需要完善一下我们前端的异常监控代码，还需要单独开发一个异常管理系统。好在我们有现成的轮子可以使用，Sentry 是就是一个非常出色的服务，今天我们就来了解一下如何使用 Sentry 进行异常监控的。

### 创建前端项目

我们以 Vue 的项目为例，首先使用 `vue-cli` 创建一个项目

```bash
vue create hello-world
```

然后在在项目里手动抛出一个异常，以便后续异常上报。

在 `/src/components/HelloWorld.vue` 文件第 40 行抛出一个错误：

![异常](../img/assets/sentry_error.png)

### 注册

我们可以使用 Sentry 提供的在线服务来帮助我们进行异常监控。首先我们需要注册一个帐号，[注册地址](https://sentry.io/signup)

![注册](../img/assets/sentry_register.png)

如上图所示，填写姓名、组织、邮箱、密码后就可以了。

### 创建 Sentry 项目

创建一个 Sentry 非常简单，只需要选中我们想要类型的项目就可以了：

![创建项目](../img/assets/sentry_project.png)

### 前端项目相关配置

按照创建完项目后 Sentry 的提示，在我们的 Vue 项目中进行配置即可：

- 安装 `@sentry/browser`

```bash
# Using npm
$ npm install @sentry/browser

# Using yarn
$ yarn add @sentry/browser
```

- 安装 `@sentry/integrations`

```bash
# Using npm
$ npm install @sentry/integrations

# Using yarn
$ yarn add @sentry/integrations
```

- 在 app.js 中添加代码

```js
import * as Sentry from '@sentry/browser';
import { Vue as VueIntegration } from '@sentry/integrations';

Sentry.init({
  dsn: 'https://66ed9457c0e5405397f465b4f9223aa1@o381494.ingest.sentry.io/5208892', // 创建项目后会生成 dsn
  integrations: [new VueIntegration({Vue, attachProps: true})],
});
```

OK，配置完成。

### 开发环境

启动我们的项目，然后打开我们的前端页面。

```bash
npm run serve
```

打开控制台，发现浏览器并没有打印我们的错误，可见 Sentry 已经捕获到错误了。 若需要浏览器在控制台打印错误，可通过设置 `integrations` 的 `logErrors` 选项为 `true`。

然后看一下我们的 Sentry 项目管理页面，可以看到已经收集到异常了：

![sentry_issues](../img/assets/sentry_issues.png)

点开错误详情看一下：

![sentry_issues](../img/assets/sentry_error_detail.png)

可以看到，错误信息收集的十分完整，不仅有错误的堆栈信息，还有浏览器环境相关信息等等。

现在已经可以愉快的收集异常了，是不是非常简单 ~~

### 生成环境

生产环境中我们的代码肯定都是经过压缩的，那我们看一下在生产环境中，Sentry 的表现是怎样的。可以本地通过 docker 启动一个 nginx 服务来部署我们的代码，这里就不详细介绍了。项目部署之后，再去看一下我们收集到的异常详细信息：

![生产环境错误详情](../img/assets/sentry_prod_error_1.png)
![生产环境错误详情](../img/assets/sentry_prod_error_2.png)

只能看到错误发生在哪个组件的生命周期钩子里，错误的堆栈信息是代码压缩后的代码堆栈信息，这对我们对错误进行定位就不是很方便了，为了解决这个问题，我们需要把webpack打包生成的 js 文件以及 sourceMap 文件上传到 Sentry 上面，这样，Sentry 就能够帮助我们准确定位了。

我们需要做以下操作：

#### 1. 安装 @sentry/webpack-plugin

```bash
# Using npm
$ npm install --save-dev @sentry/webpack-plugin

# Using yarn
$ yarn add --dev @sentry/webpack-plugin
```

#### 2. 修改 webpack 配置 和 release 相关版本配置

- 新增 `/src/settings.js` 文件，方便进行 release 版本管理。

```js
module.exports = {
  version: 'v1.0.0',
}
```

- 在 `/src/main.js` 中初始化 Sentry 时添加 release 相关配置。

```js
import settings from './settings.js'

Sentry.init({
  release: settings.version,
  dsn: 'https://66ed9457c0e5405397f465b4f9223aa1@o381494.ingest.sentry.io/5208892', // 创建项目后会生成 dsn
  integrations: [new VueIntegration({Vue, attachProps: true, logErrors: process.env.NODE_ENV === "production" ? false : true})],
});
```

- 新增 `.sentryclirc` 文件

```code
[defaults]
url=https://sentry.io/
org=org
project=org

[auth]
token=token
```

对文件中的参数进行一下说明：

使用 Sentry 线上服务的话，url 设置为 `https://sentry.io/`

org 和 project 可按下图进行配置：

![sentryclirc配置](../img/assets/sentry_sentryclirc.png)

token 按以下步骤获取：

1. 点击红框标出的 setting
![sentryclirc配置](../img/assets/sentry_token_1.png)

2. 点击红框标出的 Auth Tokens
![sentryclirc配置](../img/assets/sentry_token_2.png)

3. 点击红框标出的 Create New Token
![sentryclirc配置](../img/assets/sentry_token_3.png)

4. 选中 project:write
![sentryclirc配置](../img/assets/sentry_token_4.png)

5. 复制 token 到 `.sentryclirc` 文件中
![sentryclirc配置](../img/assets/sentry_token_5.png)

- 新增 `vue.config.js` 文件

```js
"use strict";
const SentryCliPlugin = require("@sentry/webpack-plugin");
const settings = require("./src/settings.js");

module.exports = {
  configureWebpack: {
    plugins: [
      new SentryCliPlugin({
        release: settings.version,
        include: "./dist",
        ignore: ["node_modules", "vue.config.js"],
      }),
    ],
  },
};
```

#### 3. 重新打包部署

执行打包命令:

```bash
npm run build
```

可以在命令行中看到我们上传了打包后生成的 js 文件 和 map 文件：

![上传map](../img/assets/sentry_upload.png)

上传后的文件可以在 Sentry 的管理页面中 Releases 相关版本详情中看到：

![上传map](../img/assets/sentry_upload_2.png)

部署后，刷新页面，然后进入到 error 详情中查看，如下：

![map error](../img/assets/sentry_map_error.png)

发现可以精准的对错误信息进行定位，完美 ~~

### 私有化部署

Todo
