---
title: 'Canvas学习笔记四：路径'
entitle: canvas-path
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1545469530
comments: true
date: 2018-12-22 17:05:30
tags:
- Canvas
keywords:
description: 通过路径来描边和填充
photos:
- /img/tags/canvas.gif
---

除了绘制矩形的两个 API 外，Canvas 的绘图环境还提供了其它一些方法用于绘制更加复杂的图形。这些方法都是基于路径 (path) 的。

### 图形路径 (rect & arc)

我们可以先看一个使用 context 提供的 API 绘制简单图形路径 (矩形和圆弧) 的基本示例：[Canvas Path](https://codepen.io/haohaio/pen/JwJBNz)

通过示例，我们可以看到绘制图形的基本步骤如下：

- context 调用 beginPath() 方法来开始一段新的路径。
- context 使用 rect() 或 arc() 方法分别创建矩形及弧形路径。
  - rect() 方法创建的路径是封闭的。
  - arc() 方法创建的路径，是不封闭的。除非是个圆形路径。可调用 closePath() 方法封闭路径。
- context 调用 stroke() 或 fill() 方法，对路径进行描边或填充。

其中对 arc() 方法进行一下详细介绍：

```javascript
/**
 * x: 圆的中心的 x 坐标
 * y: 圆的中心的 y 坐标
 * radii: 圆的半径
 * startAngle: 起始角，以弧度计。 (弧的圆形的三点钟位置是 0 度)
 * endAngle: 结束角，以弧度计。
 * counterclockwise: 规定应该逆时针还是顺时针绘图。false = 顺时针，true = 逆时针。默认值为 false。
 */
arc(x, y, radii, startAngle, endAngle, counterclockwise)
```

> rect() 方法绘制路径时，总是顺时针方向的。(我们可以自定义方法来支持逆时针方向绘制矩形路径)

#### 子路径

Canvas 之中只能有一条路径存在，Canvas 规范将其称为 “当前路径”（current path）。然而，这条路径可以包含许多子路径（subpath）。而子路径
是由两个或更多的点组成的。

> 如果在当前路径中存在子路径的情况下调用 arc() 方法, 那么此方法就会从子路径的终点向圆弧的起点画一条线。

#### 填充路径时的 “非零环绕规则” (nonzero winding rule)

如果当前路径是循环的，或是包含多个相交的子路径，那么 Canvas 的绘图环境就需要判断，当 fill() 方法被调用时，应该如何进行填充。Canvas 是使用 “非零环绕规则” 来进行判断的：

- 对于路径中任意指定区域，从该区域内部画一条足够长的线段，使此线段的完全落在路径范围之外。
- 然后，将计数器初始化为 0，每当这个线段与路径上的直线或曲线相交时，就改变计数器的值，如果是与路径顺时针相交时，那么计数器就加 1， 如果是与路径逆时针相交时，那么计数器就减 1。
- 如果计数器最终值不是 0，那么此区域就在路径范围里面，在调用 fill() 方法时，浏览器就会对其进行填充。如果最终值是 0，那么此区域就不在路径范围内，浏览器就不会对其进行填充。

具体情况如图所示：
![nonzero_winding.png](https://upload-images.jianshu.io/upload_images/1692994-43f8eb98e08b6474.png)
