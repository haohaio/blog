---
title: Web动画之animation
entitle: 'web-animation-animation'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1544578821
comments: true
date: 2018-12-12 09:40:21
tags:
keywords:
description:
photos:
---

在 [Web动画之transition](/front-end/web-animation-transition-1544336906.html) 中我们可以了解到 transition 可以把一系列属性从一个状态变到另一个状态。CSS动画还提供了一种创建动画的方式，而且功能更加丰富。animation 能把一系列属性从一个状态变到另一个状态，再变到第三个状态，然后一直这么变下去。此外，还能重播动画，鼠标移到动画上时停止动画，甚至在动画结束时还能倒播动画。接下来我们就来了解一下 animation。

animation 比 transition 复杂一些，不过有个额外的好处：无需触发就能开始动画。

# Animation 简介

> 与 transition 一样，IE9 及之前的版本也不支持 animation。

创建 animation 的过程主要分成以下两步：

## 定义动画

即设置 keyframe (关键帧, 指展示画面的一帧)，列出要变化的CSS属性。基本结构如下：

```css
@keyframes animationName {
  from {
    /* 在这里列出CSS属性 */
  }

  to {
    /* 在这里列出CSS属性 */
  }
}
```

> keyframes 不是CSS属性，而是@规则。此外，CSS还有几个@规则：在样式表中加载另外一个样式表的 @import 语句；为不同媒介类型定义样式的 @media。

- 首先在 @keyframes 后面定义一个名称，即动画的名称 (e.g. fadeIn、fadeOut)。
- 然后至少添加两个关键帧。在上面的示例中，关键字 from 用于创建起始关键帧，关键字 to 用于创建结束关键帧。在每个关键帧中可以添加一个或多个CSS属性，与定义样式一样。例如： [fadeIn](https://codepen.io/haohaio/pen/KbKbEN)
- 不仅只能定义两个关键帧，还可以用百分比值定义多个关键帧。百分比表示在整个动画过程的什么位置发生变化。 例如：[growAndGlow](https://codepen.io/haohaio/pen/BvazyJ)
- 百分比值的用法还有个特别的技巧：使用不同的百分比值定义相同的CSS属性。首先，当动画播放到摸一会时刻时暂停，然后再继续 (e.g. [glow](https://codepen.io/haohaio/pen/jXbqNO))。还可以用来不同时间段使用相同的CSS属性(e.g. [glow](https://codepen.io/haohaio/pen/REWare))

> 可以把关键字 form 换成 0%，把关键字 to 换成 100%。

## 应用动画

定义好动画后，若想播放动画，还需将动画应用到页面的元素上。

- 直接将动画加到动画的样式中，则动画在页面加载时就会播放(上面的例子都是如此)。
- 此外，可以把动画添加到某个伪类中 (e.g. :hover、:active、:target或:focus)，还可以把动画添加到类样式中，在需要的时候使用js动态应用那个类样式。

CSS提供了几个 animation 相关的属性，用于控制如何以及何时播放动画。

> 有些属性与 transition 基本相同，这里不做过多解释

- animation-name
  - 以CSS关键字做动画名称时，放在引号里能避免冲突
  - 可以对应多个动画的名称，来让一个元素应用多个动画
- animation-duration
- animation-timing-function
  - 可以为每个关键帧设置不同的时序函数
- animation-dealy
- animation-iteration-count (动画运行次数)(e.g. animation-iteration-count: 10;)
  - 设为关键字 infinite 时，可无限次运行动画
- animation-direction
  - 当动画多次执行时，如有需要，可设为 alternate 关键字，动画会在奇数时正向播放，偶数时反向播放。
- animation-fill-mode
  - 可设为 forwords 关键字，当动画结束时让元素显示成动画结束后的样子。

### animation属性的简写形式

- 名字和持续时间是必写的。
- 多个动画时，可用逗号分开。

### 暂停动画

若有需要可使用伪类或js设置 animation-play-state (running|paused) 属性为 paused 来暂停动画。
  