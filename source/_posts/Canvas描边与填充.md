---
title: Canvas学习笔记三：描边与填充
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
description: 通过描边与填充来绘制图形
photos:
- /img/tags/canvas.gif
---

Canvas 的绘制操作主要分为两种：描边与填充。

### 坐标系统

在进行绘制之前，我们还要先需要了解一下 Canvas 的坐标系统。只有先通过坐标确定了位置，Canvas 绘图环境才知道在哪里进行绘制。

在默认情况下，坐标系统是以 canvas 的左上角为原点，X 坐标向右方增长，Y 坐标向下方延伸。

但Canvas 的坐标系统并不是固定的，我们还可以对坐标系统进行平移、旋转、缩放等操作。这里先不做详细介绍

### 文本的绘制

Canvas 的绘图环境提供了下面两个 API 来直接绘制文本：

- strokeText(string text, double x, double y)
- fillText(string text, double x, double y)

### 矩形的绘制

Canvas 的绘图环境中仅有两个方法可以用来立即绘制图形 (其它都需要路径)，用于有关矩形的绘制：

```javascript
- strokeRect(double x, double y, double width, double height)
- fillRect(double x, double y, double width, double height)
```

还有一个与矩形相关的 API，可用来清除矩形区域：

```javascript
- clearRect(double x, double y, double width, double height)
```

示例：[Canvas Rect](https://codepen.io/haohaio/pen/EGZKvr)

### 描边与填充的样式 (strokeStyle & fillStyle)

#### 颜色

Canvas 默认使用的颜色为不透明的黑色。在绘制矩形的示例中通过 strokeStyle 属性修改的描边的颜色，通过 fillStyle 属性修改了填充的颜色。代码如下：

```javascript
// 设置描边颜色
context.strokeStyle = 'red';
// 设置填充颜色
context.fillStyle = 'rgba(0, 0, 255, .5)';
```

可看到左边矩形的边框遮盖了文本，这是因为边框用的是不透明色。

strokeStyle 与 fillStyle 的属性值可以是任意有效的 CSS 颜色字符串。可以用 RGB、RGBA、HSL、HSLA 以及十六进制 RGB 标注法来指定，还可以通过 ‘red’、‘yellow’ 这样的颜色名称来指定。

#### 渐变色

Canvas 元素支持线性 (linear) 渐变和放射 (radial) 渐变。

- 线性渐变
  我们可以通过 context 调用 createLinearGradient(double x1, double y1, double x2, double y2) 方法来创建线性渐变。需要向该方法传入两个点的 x、y 坐标，两点之间的连线就是 canvas 建立颜色渐变效果的依据。可通过该方法创建返回的 LinearGradient 实例调用 addColorStop(double stop, string color) 来向该渐变色添加颜色停止点。然后将该实例指定为 fillStyle 进行绘制。

  示例：[Canvas Linear Gradient](https://codepen.io/haohaio/pen/jXyawX)

- 放射渐变
  我们可以通过 context 调用 createRadialGradient(double x1, double y1, double d1,double x2, double y2, double d2) 方法来创建放射渐变需要指定两个圆形 (x、y 指定圆心，d 指定半径)，它们表示某个圆锥的起止部位。该方法会返回一个 RadialGradient 实例。

  示例：[Canvas Radial Gradient](https://codepen.io/haohaio/pen/OrpXRx)

#### 图案

除了颜色和渐变色，Canvas 元素也允许使用图案来对图形和文本进行描边和填充。这里的图案可以是 image元素、canvas元素 或 video元素。

可以用 createPattern(pattern, string repetition) 来创建图案。第一个参数指定了图案所用的图像，第二个参数指定如何重复图案：repeat | repeat-x | repeat-y | no-repeat。

示例：[Canvas Pattern](https://codepen.io/haohaio/pen/yGMQPG)
