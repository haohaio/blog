---
title: '浅谈移动端H5页面的适配方案'
entitle: html5-layout
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1546855811
comments: true
date: 2019-01-07 18:10:11
tags:
- HTML5
keywords:
description:
photos:
- /img/tags/html5.jpg
---

由于现在的移动设备屏幕尺寸众多，所以要做好适配真的是一个让人头痛的问题。今天就来了解一下如何解决这个问题。

在介绍方案之前，我们要首先要对 viewport 的 meta 标签有一个大概的了解，其主要用来告诉浏览器如何规范的渲染 Web 页面。我们经常可以在页面中看到这样的代码：

```html
<!-- 将视窗的宽度设置为设备宽度。网页不缩放，不允许用户缩放。页面占满整个屏幕 (适配“刘海”屏)。-->
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=1, user-scalable=no, viewport-fit=cover"/>
```

### 方案一：浏览器自动缩放

首先来介绍一种最简单粗暴的一种解决方案：

假如我们设计稿的宽度是 `750px`, 那么我们可以将 viewport 的 meta 标签设置为这个样子：

```html
<!-- 将视窗的宽度设置为 750px -->
<meta name="viewport" content="width=750">
```

然后写 CSS 样式关于尺寸的值时，所有的值都保持与设计稿的大小一致，这样我们就可以适配各个不同尺寸的设备啦~~

不信的话，可以复制代码在浏览器里悄悄：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=750">
  <title>固定视窗宽度，浏览器自动缩放</title>
  <style>
    * {
      margin: 0;
    }

    div {
      height: 80px;
      line-height: 80px;
      margin-bottom: 80px;
      background: #000;
      color: #fff;
      font-size: 48px;
      text-align: center;
    }

    #first {
      width: 400px;
    }

    #second {
      width: 600px;
    }

    #third {
      width: 750px;
    }
  </style>
</head>

<body>
  <div id="first">400px</div>
  <div id="second">600px</div>
  <div id="third">750px</div>
</body>

</html>
```

这种做法，就是将视窗设置成固定的宽度，让浏览器自动缩放进行适配。但这种做法有一个很明显的弊端，就是不支持媒体查询，当我们需要跟聚设备宽度做一些样式的定制时，这种方案就做不到了。其原因就是我们将视窗设置成了固定的宽度，媒体查询时获得的宽度始终都是 `750px` 了。。。

简单来说，当我们的页面没有那么精致的时候，这种方案就可以满足我们的要求了~~

### 方案二：动态设置缩放比例

此外，我们还可以将视窗的宽度设为 `device-width`，动态设置缩放比例来进行适配。但这种方案其实与方案一是一样的，还是不支持媒体查询。因为缩放也会影响视窗的尺寸。这种方案相对于方案一来说，兼容性可能会更好一点，但具体兼容性没有进行测试，不太清楚~~

动态设置缩放比例的代码如下：

```javascript
var resizeEvt = 'onorientationchange' in window ? 'orientationchange' : 'resize';

var recalc = function() {
  var metaEl = document.querySelector('meta[name="viewport"]');
  var designWidth = 750; // 设计稿的宽度
  var scale = document.documentElement.clientWidth / designWidth; // 视窗宽度与设计稿宽度的比例

  if (!metaEl) {
    metaEl = document.createElement('meta');
    metaEl.setAttribute('name', 'viewport');
    metaEl.setAttribute('content', 'width=device-width, initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no', 'viewport-fit=cover');
    document.documentElement.firstElementChild.appendChild(metaEl);
  } else {
    metaEl.setAttribute('content', 'width=device-width, initial-scale=' + scale + ', minimum-scale=' + scale + ', maximum-scale=' + scale + ', user-scalable=no', 'viewport-fit=cover');
  }
};

if (document.addEventListener) {
  // 监听屏幕旋转事件
  window.addEventListener(resizeEvt, recalc, false);
  // 监听 dom 内容加载完毕事件
  document.addEventListener('DOMContentLoaded', recalc, false);
}
```

> document.documentElement.clientWidth 还是始终都是 750px。

### 方案三：使用 rem 进行布局

有时为了让页面更加精致，我们还是不能将视窗设置成固定的宽度。一般情况下，我们通过设置告诉浏览器使用它的理想视口，即将视窗的宽度设为设备的宽度，并不进行缩放：

> 苹果引入了理想视口的概念，它是对设备来说最理想的布局视口尺寸。理想视口中的网页用户最理想的宽度，用户进入页面的时候不需要缩放。

```html
<!-- 解决各种浏览器兼容问题的 理想视口 设置 -->
<meta name="viewport" content="width=device-width, initial-scale=1">
```

CSS3 引入了 rem，rem 是一种 CSS 单位。简单来讲 rem 就是相对于根元素 `<html>` 的 font-size 来计算尺寸大小。

```css
/* 设置根元素 html 的 font-size 为 16px */
html {
  font-size: 16px;
}

/* 相对于根元素字体大小，所以为 32px */
p {
  font-size: 2rem;
}
```

基于此，我们可以使用 rem 布局来解决移动端 H5 页面的适配问题。即把与元素尺寸有关的 CSS 属性都以 rem 作为单位，通过计算为不同设备的根元素设置不同的 fontSize，这样页面在不同设备下就能保持一致的网页布局。

动态设置根元素的 fontSize 大小的代码简单如下：

```javascript
var docEl = document.documentElement; // 获取根元素，即 <html> 元素
var designWidth = 750; // 设计稿的宽度
var baseFontSize = 100; // 写代码时所基于的根元素的 fontsize 大小。为了便于计算，设为 100
var resizeEvt = 'onorientationchange' in window ? 'orientationchange' : 'resize';
// 重新计算根元素的 fontsize
var recalc = function () {
  var clientWidth = docEl.clientWidth; // 视窗宽度，即 viewport 的 meta 标签中 width 所设置的值 (device-width) * 缩放比例

  if (clientWidth) {
    docEl.style.fontSize = baseFontSize * (clientWidth / designWidth) + 'px';
  }
};

if (document.addEventListener) {
  // 监听屏幕旋转事件
  window.addEventListener(resizeEvt, recalc, false);
  // 监听 dom 内容加载完毕事件
  document.addEventListener('DOMContentLoaded', recalc, false);
}
```

此外，我们还可以通过 CSS 预处理器来帮助我们将设计稿中的 px 转为 rem，比如在 sass 中封装一个方法：

```scss
$base-font-size: 100;

// 计算 px 转 rem
@function px2rem($px) {
  @return $px / $base-font-size * 1rem
}
```

### 方案四：使用 vw 进行布局

`vw` 是基于Viewport视窗的长度单位，这里的视窗（Viewport）指的就是浏览器可视化的区域，而这个可视区域是 `window.innerWidth/window.innerHeight` 的大小。

- vw：是 Viewport's width 的简写，1vw 等于 window.innerWidth的1%
- vh：和 vw 类似，是Viewport's height的简写，1vh 等于window.innerHeihgt的1%
- vmin：vmin的值是当前 vw 和 vh 中较小的值
- vmax：vmax的值是当前 vw 和 vh 中较大的值

> 假如我们设计稿的宽度是 `750px`, 那么 1vw 就是 7.5px

我们依然是将 viewport 的 mate 标签设置为这个样子：

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

我们直接将设计稿的中 px 换算成 vw 来设置样式后，就可以完成适配了，而不需要再像方案三中动态的修改 html 元素的 fontSize 了~~

这种方案与方案三的方案思想是一致的，都是进行等比例缩放。相对于方案三来说，这种方案兼容性较差一些，但网上也有一些方法来处理这问题，这里就不详细介绍了。。。
