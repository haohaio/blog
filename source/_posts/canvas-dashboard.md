---
title: 'Canvas学习笔记六：绘制仪表盘'
entitle: canvas-dashboard
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1546394525
comments: true
date: 2019-01-02 10:02:05
tags:
- CSS
keywords:
description: 使用 Canvas 绘制一个仪表盘
photos:
- /img/2018/canvas.gif
---

基于之前所学的关于 Canvas 的知识，来完成一个综合实例，绘制一个刻度仪表盘。

### 一、绘制网格线

```javascript
function drawGrid(color, stepX, stepY) {
  context.save();
  context.strokeStyle = color;
  context.lineWidth = 0.5;

  for (var i = stepX + 0.5; i < context.canvas.width; i += stepX) {
    context.beginPath();
    context.moveTo(i, 0);
    context.lineTo(i, context.canvas.height);
    context.stroke();
  }

  for (var i = stepY + 0.5; i < context.canvas.height; i += stepY) {
    context.beginPath();
    context.moveTo(0, i);
    context.lineTo(context.canvas.width, i);
    context.stroke();
  }
}

drawGrid('lightgray', 10, 10);
```

### 二、绘制仪表盘中心圆点

```javascript
function drawCentroid() {
  context.beginPath();
  context.save();
  context.strokeStyle = 'rgba(0, 0, 0, .5)';
  context.fillStyle = 'rgba(80, 190, 240, .6)';
  context.arc(canvas.width / 2, canvas.height / 2, 10, 0, Math.PI * 2, false);
  context.stroke();
  context.fill();
  context.restore();
}
```

### 三、绘制仪表盘指针

```javascript
function drawCentroidGuidewire() {
  // 计算得到刻度指示点坐标
  var angle = -Math.PI / 4;
  var radius = 205;
  var endPoint = {
        x: canvas.width / 2 + radius * Math.cos(angle),
        y: canvas.height / 2 + radius * Math.sin(angle)
      };

  context.save();

  context.strokeStyle = 'goldenrod';
  context.fillStyle = 'rgba(250, 250, 0, 0.6)';

  // 绘制刻度指示点
  context.beginPath();
  context.strokeStyle = 'rgba(100, 140, 230, 0.9)';
  context.arc(endPoint.x, endPoint.y, 5, 0, Math.PI * 2, false);
  context.fill();
  context.stroke();

  // 绘制中心原点与指示点连接线
  context.beginPath();
  context.moveTo(canvas.width / 2 , canvas.height / 2);
  context.lineTo(endPoint.x, endPoint.y);
  context.stroke();

  context.restore();
}
```

### 四、绘制外层圆圈

```javascript
function drawRing() {
  drawRingOuterCircle();

  context.strokeStyle = 'rgba(0, 0, 0, .1)';
  context.arc(canvas.width / 2, canvas.height / 2, 185, 0, Math.PI * 2, false);
  context.fillStyle = 'rgba(100, 140, 230, .1)';
  context.fill();
  context.stroke();
}

function drawRingOuterCircle() {
  context.shadowColor = 'rgba(0, 0, 0, .7)';
  context.shadowOffsetX = 3;
  context.shadowOffsetY = 3;
  context.shadowBlur = 6;
  context.strokeStyle = 'rgba(100, 140, 230, .5)';
  context.beginPath();
  context.arc(canvas.width / 2, canvas.height / 2, 205, 0, Math.PI * 2, true);
  context.stroke();
}
```

### 五、绘制内层圆

```javascript
function drawTickInnerCircle() {
  context.sava();
  context.beginPath();
  context.strokeStyle = 'rgba(0, 0, 0, .1)';
  context.arc(canvas.width / 2, canvas.height / 2, 175, 0, Math.PI * 2, false);
  context.stroke();
  context.restore();
}
```

### 六、绘制刻度线

```javascript
function drawTicks() {
  context.save();

  for (var angle = 0, cnt = 0; angle < Math.PI * 2;
    angle += Math.PI / 64, cnt++) {
    drawTick(angle, 185, cnt++);
  }

  context.restore();
}

function drawTick(angle, radius, cnt) {
  context.beginPath();
  var tickWidth = cnt % 4 === 0 ? 10 : 5;
  context.moveTo(canvas.width / 2 + Math.cos(angle) * (radius - tickWidth),
      canvas.height / 2 + Math.sin(angle) * (radius - tickWidth));
  context.lineTo(canvas.width / 2 + Math.cos(angle) * (radius),
      canvas.height + Math.sin(angle) * (radius));
  context.strokeStyle = 'rgba(100, 140, 230, 0.7)';
  context.stroke();
}
```

### 七、绘制标注

```javascript
function drawAnnotations() {
  context.save();
  context.fillStyle = 'rgba(0, 0, 230, 0.9)';
  context.font = '12px Helvetica';

  for (var angle = 0; angle < 2 * Math.PI; angle += Math.PI / 8) {
    context.beginPath();
    context.fillText((angle * 180 / Math.PI).toFixed(0),
      canvas.width / 2 + Math.cos(angle) * (185 - 10 * 2),
      canvas.height / 2 - Math.sin(angle) * (185 - 10 * 2));
  }
  context.restore();
}
```