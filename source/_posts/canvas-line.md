---
title: 'Canvas学习笔记五：线段'
entitle: canvas-line
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1546052469
comments: true
date: 2018-12-29 11:01:09
tags:
- CSS
keywords:
description:
photos:
- /img/2018/canvas.gif
---

Canvas 绘图环境提供了两个可以用来创建线段的方法：

- moveTo(x, y)
  向当前路径增加一条子路径，该子路径只包含一个点，就是参数传入的点。
- lineTo(x, y)
  若当前路径没有子路径，那么这个方法就与 moveTo() 方法一样。如果当前路径中存在子路径，那么该方法会将会将参数传入的点加入到子路径中。

> 绘制线段时还是需要创建完路径后，调用 stroke() 方法才能绘制。

#### 线段与像素边界

- 如果在像素边界绘制一条 1 像素宽的线段，那么 Canvas 的绘图环境对象会试着将分别将半个像素画在边界中线的两边。然而，在一个整像素范围内，绘制半个像素宽的像素是不可能的，所以边界两个方向的半个像素都被扩展为一个像素。所以最后绘制的线段宽度为 2 个像素。
- 如果将线段绘制在一个像素的中间，那么刚好绘制的就是 1 像素宽的线段。

[示例](https://codepen.io/haohaio/pen/LMzmWP)

#### 线段端点的绘制

我们可以是使用 Canvas 绘图环境的 lineCap 属性来控制线段端点的绘制。可取值为：

- butt：默认值，无特殊处理。
- round：在端点处多画一个半圆，其半径等于线宽的一半。
- square：在端点处多画一个矩形，长度与线宽一致，宽度等于线宽的一半。

![lineCap](https://upload-images.jianshu.io/upload_images/1692994-5df1aadc7538c769.png)

#### 线段连接点的绘制

我们可以是使用 Canvas 绘图环境的 lineJoin 属性来控制线段连接点的绘制。可取的值：bevel, round, miter，默认值是 miter，具体如下图所示：

![lineJoin](https://upload-images.jianshu.io/upload_images/1692994-a85619d9ed578f91.png)

当取值为 miter 时，还可以指定一个 miterLimit 属性，它表示对斜接线的长度与二分之一线宽的比值做出限制处理。

斜接线指的是在两条线交汇处内角和外角之间的距离。
![meterLimit](https://upload-images.jianshu.io/upload_images/1692994-0130bd65e1f03d98.gif)

若比值超过了设置的限制值，浏览器就会以 bevel 的方式来绘制线段的连接点。默认值为 10.0。

![miterLength](https://upload-images.jianshu.io/upload_images/1692994-145648d3f4b937bf.gif)