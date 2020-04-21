---
title: "[JavaScript LeetCode]7. 整数反转"
entitle: "leetcode-reverse-integer"
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1545404912
comments: true
date: 2018-12-21 23:08:32
tags:
  - LeetCode
keywords:
description: Reverse Integer
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode.com/problems/reverse-integer/)

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

示例:

```code
输入: 123
输出: 321

输入: -123
输出: -321

输入: 120
输出: 21
```

> 假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−2^31,  2^31 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

### 解法一

```js
var reverse = function (x) {
  let rev = 0;
  while (x !== 0) {
    let pop = x % 10;
    x = parseInt(x / 10);

    if (
      rev > parseInt((Math.pow(2, 31) - 1) / 10) ||
      (rev === parseInt((Math.pow(2, 31) - 1) / 10) && pop > 7)
    ) {
      return 0;
    }

    if (
      rev < parseInt(Math.pow(-2, 31) / 10) ||
      (rev === parseInt(Math.pow(-2, 31) / 10) && pop < -8)
    ) {
      return 0;
    }

    rev = rev * 10 + pop;
  }

  return rev;
};
```

### 解法二

```js
var reverse = function (x) {
  var isNegative = x < 0 ? true : false;
  var target = (isNegative ? -x : x).toString().split("").reverse().join("");
  target = isNegative ? -target : +target;
  var outRange = target < Math.pow(-2, 31) || target > Math.pow(2, 31) - 1;

  return outRange ? 0 : target;
};
```
