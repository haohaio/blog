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
description: Canvas的基本绘制操作
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

### 渐变色和图案

#### 渐变色

Canvas 元素支持线性 (linear) 渐变和放射 (radial) 渐变。

##### 线性渐变

我们可以通过 context 调用 createLinearGradient(double x1, double y1, double x2, double y2) 方法来创建线性渐变。需要向该方法传入两个点的 x、y 坐标，两点之间的连线就是 canvas 建立颜色渐变效果的依据。可通过该方法创建返回的 LinearGradient 实例调用 addColorStop(double stop, string color) 来向该渐变色添加颜色停止点。然后将该实例指定为 fillStyle 进行绘制。

示例代码：[Linear Gradient](https://codepen.io/haohaio/pen/jXyawX)

##### 放射渐变

我们可以通过 context 调用 createRadialGradient(double x1, double y1, double d1,double x2, double y2, double d2) 方法来创建放射渐变需要指定两个圆形 (x、y 指定圆心，d 指定半径)，它们表示某个圆锥的起止部位。该方法会返回一个 RadialGradient 实例。

示例代码：[Radial Gradient](https://codepen.io/haohaio/pen/OrpXRx)

#### 图案

除了颜色和渐变色，Canvas 元素也允许使用图案来对图形和文本进行描边和填充。这里的图案可以是 image元素、canvas元素 或 video元素。
