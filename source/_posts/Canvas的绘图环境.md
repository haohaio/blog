---
title: Canvas学习笔记二：Canvas的绘图环境
entitle: 'canvas-context'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1545031997
comments: true
date: 2018-12-17 15:33:17
tags:
- CSS
keywords:
description:
photos:
- /img/2018/canvas.gif
---

canvas 元素仅仅是为了充当绘图环境对象的容器而存在的，该环境对象提供了全部的绘制功能。本篇文章我们只关注 2d 绘图环境。

### 2d 绘图环境

Canvas 的 2d 绘图环境对象提供了功能强大的API，可以用来绘制图形与文本，显示并修改图像等等。本篇文章我们就对 canvas 绘图环境的属性和一些方法做一些简单的了解。

下表列出了绘图环境对象的所有属性：

| 属性                    | 简介                                                                                 |
| :---------------------- | :----------------------------------------------------------------------------------- |
| canvas                  | 指向绘图环境所属的canvas对象                                                         |
| fillstyle               | 指定该绘图环境在后续的图形填充操作中所使用的颜色，渐变色或方案                       |
| font                    | 设定在调用绘图环境对象的 fillText() 或 strokeText() 方法时，所使用的字型             |
| globalAlpha             | 全局透明度设定，取值范围 0~1.0                                                       |
| globalCompsiteOperation | 将某个物体绘制在其他的物体之上时采用的绘制方式                                       |
| lineCap                 | 如何绘制线段的端点，可取的值：butt、round、square，默认值是 butt                     |
| lineWidth               | 绘制线段的屏幕像素宽度。非负非无穷的 double 值，默认值是 1.0                         |
| lineJoin                | 在两条线段相交时如何绘制焦点，可取的值：bevel, round, miter，默认值是 miter          |
| miterLimit              | 如何绘制 miter 形式的线段焦点                                                        |
| shadowBlur              | 延伸的阴影效果，该值为高斯模糊方程式中的参数值，非负、非无穷的 double 值，默认值为 0 |
| shadowColor             | 阴影的颜色值                                                                         |
| shadowOffsetX           | 阴影效果的水平方向偏移量                                                             |
| shadowOffsetY           | 阴影效果的垂直方向偏移量                                                             |
| strokeStyle             | 对路径描边时所使用的绘制风格                                                         |
| textAlign               | fillText() 或 strokeText() 方法绘制的时候，所画文本的水平对齐方式                    |
| textBaseline            | fillText() 或 strokeText() 方法绘制的时候，所画文本的垂直对齐方式                    |

此外，还有两个重要的方法，用来保存及恢复当前 canvas 绘图环境的所有属性：

| 方法      | 描述                                                                                                     |
| :-------- | :------------------------------------------------------------------------------------------------------- |
| save()    | 将当前 canvas 的状态推送到一个保存 canvas 状态的堆栈顶部                                                 |
| restore() | 将 canvas 状态堆栈顶部的条目弹出。原来保存于栈顶的哪一组状态，在弹出之后，就被设置成 canvas 当前的状态了 |