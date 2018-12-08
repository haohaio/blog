---
title: Web动画之transition
entitle: ''
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
date: 2018-12-08 22:46:22
tags: css
keywords:
description:
photos:
---

目前Web动画的两大实现途径：

- CSS3动画。这类动画是当前页面动画的主力军，主要通过 transition 和 animation 两种方式来实现。
  - transition 是为页面元素设置某个需要产生动画效果的属性，如 width、height、opacity、3D旋转等。
  - animation 是关键帧动画，可以预先为动画设置多个节点，在每个节点中含有不同的状态属性，通过使用 animation 我们可以得到更为复杂的动画效果
- JavaScript动画。这类动画是通过JavaScript来动态的控制并刷新元素的各个属性值，以形成动画效果。

> 除以上两者外，我们也可以使用SVG 或 canvas等来制作动画。

一般来说，大部分简单的动画都可以使用 transition 实现。JavaScript动画则往往用于更加复杂，或是需要结合各类用户交互操作的动画效果。在运行效率方面，像jQuery这种JavaScript类库的动画效果要低于CSS3动画。而在兼容性方面，CSS3动画的兼容性要差于jQuery等类库动画，前者并不支持IE9之前的浏览器。今天就先了解一下 transition 动画。
