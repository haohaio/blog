---
title: CSS颜色值之HSL
entitle: 'css-color-hsl'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1545300077
comments: true
date: 2018-12-20 18:01:17
tags:
- CSS
keywords:
description: 使用 HSL 来设置颜色值
photos:
- /img/tags/css.jpg
---

CSS3 的 HSL 方法已经被现代浏览器广泛的支持，但在日常开发中还是很少使用，今天我们就来简单了解一下，示例代码如下：

```css
.test {
  background-color: hsl(360, 50%, 50%);
}
```

> IE8 以及更早的版本不支持使用 HSL 设置颜色值。

### RGB vs HSL

CSS3 颜色规范中说到，之所以要增加对 HSL 格式的支持，是因为以 RGB 方式来指定颜色，主要有两个缺陷：

- 它是以硬件为导向的。这种表述颜色的形式，是基于“阴极射线管”的；
- 它不直观。

### HSL 的 取值

- H：Hue(色调)。0(或360)表示红色，120表示绿色，240表示蓝色，也可取其他数值来指定颜色。取值为：0 - 360
- S：Saturation(饱和度)。取值为：0.0% - 100.0%
- L：Lightness(亮度)。取值为：0.0% - 100.0%
  
> HSL颜色当然可以转换为 RGB颜色 或 HEX颜色。