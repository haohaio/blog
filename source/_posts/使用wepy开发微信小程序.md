---
title: 使用 WePY 开发微信小程序
entitle: 'hello-wepy'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1545012645
comments: true
date: 2018-12-17 10:10:45
tags:
- 微信小程序
keywords:
description: Hello WePY
photos:
- /img/2018/wechat.png
---

## 项目搭建

```bash
# 全局安装 WePY 命令行工具
$ npm install wepy-cli -g

# 使用 WePY 命令行工具新建一个 hello-wepy 项目
$ wepy init standard hello-wepy

# 安装依赖 && 运行项目
$ cd hello-wepy && npm install && npm run dev
```

## 开发工具

### 微信开发者工具

下载 [微信开发者工具](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html?t=18121111)，安装成功后添加项目根目录为一个新项目即可。

根目录下有一个项目配置文件 - `project.config.json`，其中一些配置对应着`微信开发者工具`中的一些配置，需要注意一下

```json
{
  "description": "project description",
  "setting": {
    "urlCheck": true,
    "es6": false,
    "postcss": false,
    "minified": false
  },
  "compileType": "miniprogram",
  "appid": "touristappid",
  "projectname": "Project name",
  "miniprogramRoot": "./dist"
}
```

- urlCheck：对应不检查安全域名选项，开启。 如果已配置好安全域名则建议关闭。
- es6：对应关闭ES6转ES5选项，关闭。(未关闭会运行报错)
- postcss：对应关闭上传代码时样式自动补全选项，关闭。(某些情况下漏掉此项也会运行报错)
- minified：对应关闭代码压缩上传选项，关闭。(开启后，会导致真机 computed, props.sync 等等属性失效)

添加项目成功后，微信开发者工具展示如下：
![wepy.png](https://upload-images.jianshu.io/upload_images/1692994-c6a4def81db607ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 使用 [VS Code](https://code.visualstudio.com/) 进行开发

推荐使用 VS Code 进行开发，可通过如下步骤来设置语法高亮：

1. 在 Code 里先安装 Vue 的语法高亮插件 Vetur。
  
2. 打开任意 .wpy 文件，点击右下角的选择语言模式，默认为纯文本；在弹出的窗口中选择 .wpy 的配置文件关联，在选择要与 .wpy 关联的语言模式中选择 Vue。
  
3. 在 VS Code 编辑器设置中设置 settings.json，添加如下：

```json
"files.associations": {
    "*.wpy": "vue"
}
```  
