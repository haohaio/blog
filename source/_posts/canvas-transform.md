---
title: 'Canvas 图形拖动变形'
entitle: canvas-transform
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-10-09 15:17:29
timestamp:
tags:
  - Canvas
keywords:
description:
photos:
  - /img/tags/canvas.gif
---

> 写了一个关于 Canvas 图形拖动变形的综合性 Demo，有兴趣的话可以看一下 [canvas-drag](https://github.com/haohaio/canvas-drag)

这里拖动变形是指拖动多边形的一个角来改变多变形的形状。

这里还是以正六边形为例，直接上代码。

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
const points = []

for (let i = 0; i < 6; i++) {
  let point = {
    x: 150 + 50 * Math.cos((2 * Math.PI * i) / 6),
    y: 150 + 50 * Math.sin((2 * Math.PI * i) / 6),
  }

  points.push(point)
}

const canvas = document.getElementById('canvas1')
const ctx = canvas.getContext('2d')

paint()

function paint() {
  ctx.clearRect(0, 0, canvas.width, canvas.height)
  // 填充整个 canvas 的背景色
  ctx.fillStyle = 'rgba(0, 0, 0, 0.2)'
  ctx.fillRect(0, 0, canvas.width, canvas.height)

  ctx.beginPath()
  // 由于需要在正六边形角上绘制一个 10*10 的正方形，所以将 6 个点的横坐标和纵坐标都加 5，让点位于正方形的中心
  points.forEach((point, index) => {
    if (!index) {
      ctx.moveTo(point.x + 5, point.y + 5)
    } else {
      ctx.lineTo(point.x + 5, point.y + 5)
    }
  })
  ctx.closePath()
  ctx.strokeStyle = '#1890FF'
  ctx.lineWidth = 2
  ctx.stroke()
  ctx.fillStyle = '#D0E8FF'
  ctx.fill()

  ctx.strokeStyle = 'white'
  ctx.lineWidth = 2

  points.forEach((point) => {
    ctx.strokeRect(point.x, point.y, 10, 10)
  })
}

let isInRect = false // 判断鼠标是否在 6 个小正方形内
let dragIndex = [] // 为了处理当拖动后两个小正方形重叠时的情况，这里用数组保存
let lastDragIndex = null // 上次拖动的小正方形下标
canvas.onmousemove = function (e) {
  const x = e.offsetX
  const y = e.offsetY
  // 为了避免拖动时，图形超出 canvas 范围，这里处理一下
  const point = checkBoundary(x, y)
  isInRect = isInSixRect(point)

  // 修改一下光标样式
  if (isInRect) {
    canvas.style.cursor = 'crosshair'
  } else {
    canvas.style.cursor = 'auto'
  }

  if (dragIndex.length) {
    if (lastDragIndex) {
      for (let i = 0; i < dragIndex.length; i++) {
        const index = dragIndex[i]
        if (index === lastDragIndex) {
          updatePoint(point, lastDragIndex)
          return
        }
      }
    }

    updatePoint(point, dragIndex[0])
  }
}

function checkBoundary(x, y) {
  if (x <= 2) {
    x = 2
  }
  if (x >= canvas.width - 12) {
    x = canvas.width - 12
  }
  if (y <= 2) {
    y = 2
  }
  if (y >= canvas.height - 12) {
    y = canvas.height - 12
  }
  return { x, y }
}

// 判断点是否在矩形内比较简单，这里就不使用射线法了
function isInSixRect(mousePoint, isMouseDownEvent = false) {
  let flag = false
  points.forEach((point, index) => {
    if (
      mousePoint.x >= point.x - 2 &&
      mousePoint.x <= point.x + 12 &&
      mousePoint.y >= point.y - 2 &&
      mousePoint.y <= point.y + 12
    ) {
      if (isMouseDownEvent) {
        dragIndex.push(index)
      }

      flag = true
    }
  })

  return flag
}

// 接下来给 canvas 添加 onmousedown 事件，获得拖动的点
canvas.onmousedown = function (e) {
  const point = {
    x: e.offsetX,
    y: e.offsetY,
  }

  isInSixRect(point, true)
}

function updatePoint(point, index) {
  lastDragIndex = index
  points[index].x = point.x
  points[index].y = point.y
  paint()
}

document.onmouseup = function (e) {
  dragIndex = []
}
```

OK，Canvas 图形拖动变形到这里也可以了~
