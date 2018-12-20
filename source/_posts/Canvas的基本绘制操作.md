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

示例：[Canvas Rect](https://codepen.io/haohaio/pen/EGZKvr)

### 颜色

Canvas 默认使用的颜色为不透明的黑色。在绘制矩形的示例中通过 strokeStyle 属性修改的描边的颜色，通过 fillStyle 属性修改了填充的颜色。代码如下：

```javascript
// 设置描边颜色
context.strokeStyle = 'red';
// 设置填充颜色
context.fillStyle = 'rgba(0, 0, 255, .5)';
```

可看到左边矩形的边框遮盖了文本，这是因为边框用的是不透明色。

strokeStyle 与 fillStyle 的属性值可以是任意有效的 CSS 颜色字符串。可以用 RGB、RGBA、HSL、HSLA 以及十六进制 RGB 标注法来指定，还可以通过 ‘red’、‘yellow’ 这样的颜色名称来指定。
