---
title: "[JavaScript LeetCode]69. x 的平方根"
entitle: leetcode-sqrtx
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
comments: true
date: 2020-04-23 22:41:31
timestamp:
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/sqrtx/)

实现 `int sqrt(int x)` 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

示例:

```code
输入: 4
输出: 2

输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。
```

### 解法一

直接使用 `Math.sqrt()` 方法

```js
var mySqrt = function (x) {
  return parseInt(Math.sqrt(x));
};
```

### 解法二

暴力破解

```js
var mySqrt = function (x) {
  let res = 0;
  while (!(res * res <= x && (res + 1) * (res + 1) > x)) {
    res++;
  }

  return res;
};
```

### 解法三

二分查找法

```js
var mySqrt = function (x) {
  let left = 0;
  let right = x;
  while (left <= right) {
    let mid = Math.round((left + right) / 2);
    if (mid * mid <= x && (mid + 1) * (mid + 1) > x) {
      return mid;
    } else if (mid * mid > x) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }

  return -1;
};
```
