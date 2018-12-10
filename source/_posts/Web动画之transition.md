---
title: Web动画之transition
entitle: 'web-animation-transition'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 技术
timestamp: 1544336906
comments: false
date: 2018-12-09 14:28:26
tags: 
- css
keywords: html, css, animation
description: Web动画之transition简介
photos:
---

目前Web动画的两大实现途径：

- CSS3动画。这类动画是当前页面动画的主力军，主要通过 transition 和 animation 两种方式来实现。
  - transition (过渡) 一种简单的动画，在一定时间内从一组CSS属性变成另一组属性。
  - animation 是关键帧动画，可以预先为动画设置多个节点，在每个节点中含有不同的状态属性，通过使用 animation 我们可以得到更为复杂的动画效果。
- JavaScript动画。这类动画是通过JavaScript来动态的控制并刷新元素的各个属性值，以形成动画效果。

> 除以上两者外，我们也可以使用SVG 或 canvas等来制作动画。

一般来说，大部分简单的动画都可以使用 transition 实现。JavaScript动画则往往用于更加复杂，或是需要结合各类用户交互操作的动画效果。在运行效率方面，像jQuery这种JavaScript类库的动画效果要低于CSS3动画。而在兼容性方面，CSS3动画的兼容性要差于jQuery等类库动画，前者并不支持IE9之前的浏览器。今天就先了解一下 transition 动画。

# Transition

> IE只有10和以后的版本才支持transition

为了实现过渡效果，要满足以下几个条件：

- 两个样式 (过渡前的样式和过渡后的样式)
- transition属性 (添加到初始样式中)
- 触发器 (指促使元素在两个在两个元素之间变化的操作 (e.g. :hover))

## transition 的四个属性

> 各属性需要一一对应

- transition-property (指定需要变化的属性，可用关键字all表示所有属性)

```css
transition-property: color, background-color;
transition-property: all;
```

- transition-duration (动画持续时间)

```css
transition-duration: .5s;
transition-duration: 500ms;
```

- transition-timing-function (过渡的时序，即持续过程的速率)
  - linear (中间快，两头慢)
  - ease (开始时慢，中间加速，末尾又降速)(default)
  - ease-in (开始时慢，之后快)
  - ease-out (开始时快，结束时候慢)
  - ease-in-out (两头慢)

> 可通过演示具体比较一下各个速率的区别  [演示地址](https://codepen.io/haohaio/pen/pqoqYR)

- transition-delay (动画延时时间)

## transition属性的简写形式

- 要变化的属性和动画的持续时间是必写的。默认情况下，时序函数是ease，而且没有延迟。
- 多个要变化的属性时，可用逗号分开。

```css
transition: all 1s;
transition: background-color 1s,
            color .5s,
            border-color .5s 1s;
```

附上一个综合版的Demo [transition demo](https://codepen.io/haohaio/pen/madEJw)

