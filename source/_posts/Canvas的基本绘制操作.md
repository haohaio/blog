---
title: Canvas学习笔记三：Canvas的基本绘制操作
entitle: 'canvas-basic-drawing'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1545040014
comments: true
date: 2018-12-17 17:46:54
tags:
- CSS
keywords:
description:
photos:
- /img/2018/canvas.gif
---

### 矩形的绘制

Canvas 的绘图环境提供了如下三个方法，用于有关矩形的绘制：

- clearRect(double x, double y, double w, double h)：清除矩形
- stokeRect(double x, double y, double w, double h)：描边矩形
- fillRect(double x, double y, double w, double h)：填充矩形

| 方法                                              | 描述     |
| :------------------------------------------------ | :------- |
| clearRect(double x, double y, double w, double h) | 清除矩形 |
| stokeRect(double x, double y, double w, double h) | 描边矩形 |
| fillRect(double x, double y, double w, double h)  | 填充矩形 |