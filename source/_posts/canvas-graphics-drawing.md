---
title: "Canvas 基本图形绘制"
entitle: canvas-graphics-drawing
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-09-22 13:53:49
timestamp:
tags:
- Canvas
keywords:
description:
photos:
  - /img/tags/canvas.gif
---

### 绘制圆形

先看一下 canvas 绘制圆形的相关 API。

```js
/**
 * @param {*} x 圆心横坐标
 * @param {*} y 圆心纵坐标
 * @param {*} r 半径
 * @param {*} sAngle 起始角，以弧度计。（弧的圆形的三点钟位置是 0 度）。
 * @param {*} eAngle 结束角，以弧度计。
 * @param {*} counterclockwise 可选。规定应该逆时针还是顺时针绘图。False = 顺时针，true = 逆时针。
 */
context.arc(x, y, r, sAngle, eAngle, counterclockwise);
```

然后进行圆形的绘制：

```html
<canvas id="canvas1" width="800" height="300"></canvas>
```

```js
const canvas = document.getElementById("canvas1");
const ctx = canvas.getContext("2d");
const drawCircle = (options) => {
  const {
    x,
    y,
    r,
    sAngle,
    eAngle,
    fillStyle,
    strokeStyle,
    lineWidth,
  } = options;

  ctx.arc(x, y, r, sAngle, eAngle);

  if (fillStyle) {
    ctx.fillStyle = fillStyle;
    ctx.fill();
  }

  if (strokeStyle) {
    ctx.strokeStyle = strokeStyle;
    ctx.lineWidth = lineWidth || 2;
    ctx.stroke();
  }
};

const options = {
  x: 200,
  y: 150,
  r: 50,
  sAngle: 0,
  eAngle: 2 * Math.PI,
  fillStyle: "#D0E8FF",
  strokeStyle: "#1890FF",
};

drawCircle(options);
```

### 绘制三角形

```js
const canvas = document.getElementById("canvas1");
const ctx = canvas.getContext("2d");

const drawTriangle = (options) => {
  const { points, fillStyle, strokeStyle, lineWidth } = options;

  ctx.beginPath();
  ctx.moveTo(points[0].x, points[0].y);
  ctx.lineTo(points[1].x, points[1].y);
  ctx.lineTo(points[2].x, points[2].y);
  ctx.closePath();

  if (fillStyle) {
    ctx.fillStyle = fillStyle;
    ctx.fill();
  }

  if (strokeStyle) {
    ctx.strokeStyle = strokeStyle;
    ctx.lineWidth = lineWidth || 2;
    ctx.stroke();
  }
};

const points = [
  { x: 100, y: 150 },
  { x: 200, y: 150 },
  { x: 150, y: 50 },
];
const options = {
  points,
  fillStyle: "#D0E8FF",
  strokeStyle: "#1890FF",
};

drawTriangle(options);
```

### 绘制矩形

先看一下 canvas 绘制矩形的相关 API。

```js
/**
 * @param {*} x 矩形左上角的 x 坐标
 * @param {*} y 矩形左上角的 y 坐标
 * @param {*} width 矩形的宽度
 * @param {*} height 矩形的高度
 */
context.rect(x, y, width, height);
```

```js
const canvas = document.getElementById("canvas1");
const ctx = canvas.getContext("2d");
const drawRect = (options) => {
  const { x, y, width, height, fillStyle, strokeStyle, lineWidth } = options;

  ctx.rect(x, y, width, height);

  if (fillStyle) {
    ctx.fillStyle = fillStyle;
    ctx.fill();
  }

  if (strokeStyle) {
    ctx.strokeStyle = strokeStyle;
    ctx.lineWidth = lineWidth || 2;
    ctx.stroke();
  }
};

const options = {
  x: 50,
  y: 50,
  width: 100,
  height: 50,
  fillStyle: "#D0E8FF",
  strokeStyle: "#1890FF",
};

drawRect(options);
```

### 绘制带圆角的矩形

```js
const canvas = document.getElementById("canvas1");
const ctx = canvas.getContext("2d");
const drawRect = function (options) {
  const {
    x,
    y,
    width,
    height,
    borderRadius,
    fillStyle,
    strokeStyle,
    lineWidth,
  } = options;

  ctx.beginPath();
  ctx.moveTo(x, y + borderRadius);
  ctx.lineTo(x, y + height - borderRadius);
  ctx.quadraticCurveTo(x, y + height, x + borderRadius, y + height);
  ctx.lineTo(x + width - borderRadius, y + height);
  ctx.quadraticCurveTo(
    x + width,
    y + height,
    x + width,
    y + height - borderRadius
  );
  ctx.lineTo(x + width, y + borderRadius);
  ctx.quadraticCurveTo(x + width, y, x + width - borderRadius, y);
  ctx.lineTo(x + borderRadius, y);
  ctx.quadraticCurveTo(x, y, x, y + borderRadius);
  ctx.closePath();

  if (fillStyle) {
    ctx.fillStyle = fillStyle;
    ctx.fill();
  }

  if (strokeStyle) {
    ctx.strokeStyle = strokeStyle;
    ctx.lineWidth = lineWidth || 2;
    ctx.stroke();
  }
};

const options = {
  x: 50,
  y: 50,
  width: 100,
  height: 50,
  borderRadius: 8,
  fillStyle: "#D0E8FF",
  strokeStyle: "#1890FF",
};

drawRect(options);
```

### 绘制多边形

```js
const canvas = document.getElementById("canvas1");
const ctx = canvas.getContext("2d");

var drawPolygon = function (options) {
  const { x, y, num, r, fillStyle, strokeStyle, lineWidth } = options;
  //开始路径
  ctx.beginPath();
  var startX = x + r * Math.cos((2 * Math.PI * 0) / num);
  var startY = y + r * Math.sin((2 * Math.PI * 0) / num);
  ctx.moveTo(startX, startY);
  for (var i = 1; i <= num; i++) {
    var newX = x + r * Math.cos((2 * Math.PI * i) / num);
    var newY = y + r * Math.sin((2 * Math.PI * i) / num);
    ctx.lineTo(newX, newY);
  }
  ctx.closePath();

  if (strokeStyle) {
    ctx.strokeStyle = strokeStyle;
    ctx.lineWidth = lineWidth || 2;
    ctx.stroke();
  }
  if (fillStyle) {
    ctx.fillStyle = fillStyle;
    ctx.fill();
  }
};

drawPolygon({
  x: 150,
  y: 150,
  num: 6,
  r: 100,
  fillStyle: "#D0E8FF",
  strokeStyle: "#1890FF",
});
```
