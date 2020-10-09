---
title: "Canvas 图形拖动"
entitle: canvas-drag
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-09-23 14:50:34
timestamp:
tags:
  - Canvas
keywords:
description:
photos:
  - /img/tags/canvas.gif
---

我们在上一篇文章中学习了如何绘制了如何绘制图形，今天就来学习一下如何在 Canvas 中拖动图形。

### 图形整体拖动

首先我们来看一下图形的整体拖动。

拖动图形时，我们先要判断鼠标是否在图形中，这里我们需要使用射线法来进行判断。

> 射线法。该法中常用水平扫描线法或垂直线法来判断一点是否在区域内。假若有一疑问点 P(x，y)，要判斯它是否在多边形内，可从该疑问点向左引水平扫描线(即射线)。计算此线段与区域边界的相交次数 c。如果 c 为奇数，认为疑问点在多边形内；为偶数，则疑问点在多边形外。

这里我们以六边形为例。

- index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Canvas 图形拖动</title>
  </head>

  <body>
    <canvas id="canvas1" width="600" height="300"></canvas>

    <script src="./index.js"></script>
  </body>
</html>
```

- index.js

```js
// 以 (150, 150) 作为正六边形的中心, 半径为50，绘制一个正六边形。由于需要通过射线法进行判断，这里我们先获取正六边形 6 个点的坐标
const points = [];

for (let i = 0; i < 6; i++) {
  let point = {
    x: 150 + 50 * Math.cos((2 * Math.PI * i) / 6),
    y: 150 + 50 * Math.sin((2 * Math.PI * i) / 6),
  };

  points.push(point);
}

// 绘制正六边形
const canvas = document.getElementById("canvas1");
const ctx = canvas.getContext("2d");

paint();

function paint() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  // 填充整个 canvas 的背景色
  ctx.fillStyle = "rgba(0, 0, 0, 0.2)";
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  ctx.beginPath();
  let startPoint = points[0];
  ctx.moveTo(startPoint.x, startPoint.y);
  for (let i = 1; i < 6; i++) {
    let point = points[i];
    ctx.lineTo(point.x, point.y);
  }
  ctx.closePath();
  ctx.strokeStyle = "#1890FF";
  ctx.lineWidth = 2;
  ctx.stroke();
  ctx.fillStyle = "#D0E8FF";
  ctx.fill();
}

// 在网上搜了个射线法算法，搬过来直接用了
/**
 * @description 射线法判断点是否在多边形内部
 * @param {Object} p 待判断的点，格式：{ x: X坐标, y: Y坐标 }
 * @param {Array} poly 多边形顶点，数组成员的格式同 p
 * @return {Boolean} true: 在多边形内，false: 在多边形外
 */
function rayCasting(p, poly) {
  var px = p.x,
    py = p.y,
    flag = false;

  for (var i = 0, l = poly.length, j = l - 1; i < l; j = i, i++) {
    var sx = poly[i].x,
      sy = poly[i].y,
      tx = poly[j].x,
      ty = poly[j].y;

    // 点与多边形顶点重合
    if ((sx === px && sy === py) || (tx === px && ty === py)) {
      return true;
    }

    // 判断线段两端点是否在射线两侧
    if ((sy < py && ty >= py) || (sy >= py && ty < py)) {
      // 线段上与射线 Y 坐标相同的点的 X 坐标
      var x = sx + ((py - sy) * (tx - sx)) / (ty - sy);

      // 点在多边形的边上
      if (x === px) {
        return true;
      }

      // 射线穿过多边形的边界
      if (x > px) {
        flag = !flag;
      }
    }
  }

  // 射线穿过多边形边界的次数为奇数时点在多边形内
  return flag;
}

// 接下来给 canvas 添加 onmousemove 事件，来判断鼠标是否在多边形内，即是否可以拖动多边形

let isInGraphics = false; // 判断鼠标是否在多边形内

// 处理拖动
let startMovePoint = null;
let canMove = false; // 当鼠标在多边形内，并按下鼠标时，可以拖动多边形

canvas.onmousemove = function (e) {
  const point = {
    x: e.offsetX,
    y: e.offsetY,
  };

  isInGraphics = rayCasting(point, points);

  // 修改一下光标样式
  if (isInGraphics) {
    canvas.style.cursor = "move";
  } else {
    canvas.style.cursor = "auto";
  }
};

// 接下来给 canvas 添加 onmousedown 事件，获得拖动的开始点
canvas.onmousedown = function (e) {
  const point = {
    x: e.offsetX,
    y: e.offsetY,
  };

  if (!isInGraphics) {
    return;
  }
  startMovePoint = point;
  canMove = true;
};

// 这里需要将鼠标松开事件添加到 document 上，因为当鼠标划出 canvas 区域后再松开鼠标时，若事件添加到 canvas 上，canMove 仍为 true
document.onmouseup = function (e) {
  canMove = false;
};

function updatePoints(stopMovePoint) {
  const disX = stopMovePoint.x - startMovePoint.x;
  const disY = stopMovePoint.y - startMovePoint.y;
  // 更新拖动的开始点
  startMovePoint = stopMovePoint

  // 当拖动范围超出 canvas 范围时，不可进行拖动
  const xArr = points.reduce((acc, cur) => [...acc, cur.x], []);
  const yArr = points.reduce((acc, cur) => [...acc, cur.y], []);
  const minX = Math.min(...xArr) + disX;
  const minY = Math.min(...yArr) + disY;
  const maxX = Math.max(...xArr) + disX;
  const maxY = Math.max(...yArr) + disY;

  if(minX < 2 || maxX > canvas.width - 2 || minY < 2 || maxY > canvas.height - 2){
    return;
  }

  // 更新正六边形的 6 个点，重新进行绘制
  for (let i = 0; i < 6; i++) {
    const point = points[i];
    point.x += disX;
    point.y += disY;
  }

  paint();
}
```

OK，canvas 图形的整体拖动就是这样了~
