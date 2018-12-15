---
title: Canvas学习笔记一：初识Canvas
entitle: 'canvas-1'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1544701913
comments: true
date: 2018-12-13 19:51:53
tags:
- css
keywords:
description:
photos:
---

# 简介

canvas 元素可以说是 HTML5 元素中功能最强大的一个。我们可以通过 canvas 元素来绘制图像，不过 canvas 本身并没有绘制能力，必须通过js脚本来完成实际的绘制任务。canvas的能力主要是通过 canvas 的 context 对象表现出来的，该对象提供了用于在画布上绘图的方法和属性。

可以先看一个简单的 demo [Hello Canvas](https://codepen.io/haohaio/pen/JwGQdv)，来简单了解一下canvas。

## context 对象的获取

```javascript
var canvas = getElementById('canvas');
// '2d' 中的 'd' 必须小写
var context = canvas.getContext('2d');
```

## canvas 的 “后备内容” (fallback content)

> IE8 以及更早的版本不支持 canvas 元素。

当浏览器不支持 canvas 元素时，canvas 元素内部部分所含的文本就会显示出来，这种文本叫做“后备内容” (fallback content)。

```html
<canvas>
  Canvas not supported
</canvas>
```

另外我们可以通过简单的 js 代码来检查浏览器对 canvas 的支持性：

```javascript
var canvas = document.getElementById('canvas');

if (canvas.getContext) {
  var ctx = canvas.getContext('2d');
  // drawing code here
} else {
  // canvas-unsupported code here
}
```

## canvas 的尺寸

默认的canvas元素大小是 300 * 150 个像素。 我们可以通过指定 width 和 height 属性值来修改 canvas 元素的大小。

```html
<canvas id="canvas" width="600" height="300">
```

> 在设置 canvas 的宽度和高度时，不能使用 px 后缀。虽说支持 canvas 的浏览器普遍都允许使用 px 后缀，但是这是不被 canvas 规范所接受的。根据规范，这些属性的取值，只能是非负整数。

## canvas 元素的大小和绘图表面的大小

我们还可以通过 CSS 属性来改变 canvas 元素的大小。

```css
#canvas {
  width: 600px;
  height: 300px;
}
```

不过通过 CSS 设置 canvas 元素的大小，与通过 width、height 属性值设定，效果并不一样。

其根本原因是因为 canvas 元素实际上有两套尺寸。一个是元素本身的大小，还有一个是元素绘图表面（drawing surface）的大小。

当设置元素的 width 和 height 属性时，实际上是同时修改了元素本身的大小和元素绘图表面的大小。然而，通过 CSS 来设定 canvas 元素的大小时，只会改变元素本身的大小，而不会影响到绘图表面。当 canvas 元素的大小不符合起绘图表面的大小时，浏览器就会对绘图表面进行缩放，使其符合元素的大小。具体效果如如下面两幅图所示：

- 通过设置元素的 width 和 height 属性修改 canvas 元素大小的效果
  
![props.png](https://upload-images.jianshu.io/upload_images/1692994-170e5d8648c891f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 通过 CSS 来设定 canvas 元素的大小的效果 （浏览器自动缩放）

![css.png](https://upload-images.jianshu.io/upload_images/1692994-af21d0a29fe4c2f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以一般情况下我们都是通过 width 与 height 属性而非 CSS 来修改 canvas 元素的大小。

## canvas 元素的 API

canvas 元素只提供了2个属性与3个方法。

- canvas元素的属性

|  属性  |   类型   | 默认值 |
| :----: | :------: | :----: |
| width  | 非负整数 |  300   |
| height | 非负整数 |  150   |

- canvas元素的方法

| 方法                                   | 描述                                                                                                                                                                                                                                                                                                            |
| :------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| getContext()                           | 返回与该 canvas 元素相关的绘图环境对象。                                                                                                                                                                                                                                                                        |
| toDataURL(type, encoderOptions)        | 返回一个数据地址（data URL），可将其设定为 img 元素的 src 属性值。第一个参数指定了图像的类型，例如 image/jpeg 或 image/png，默认为 image/png。第二个参数在指定图片格式为 image/jpeg 或 image/webp的情况下, 可设定为 0 ~ 1.0 之间的 double 值，表示 JPEG 图像的显示质量，如果超出取值范围，将会使用默认值 0.92。 |
| toBlob(callback, type, encoderOptions) | 创建一个用于表示次canvas 元素图像文件的 Blob。第一个参数是一个回调函数，并传入一个 blob 对象作为参数，第二个参数为图像类型，最后一个参数为 JPEG 图像的显示质量                                                                                                                                                  |