---
title: "[JavaScript LeetCode]66. 加一"
entitle: leetcode-plus-one
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1587482217
comments: true
date: 2020-04-21 23:16:57
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

示例:

```code
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。

输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。
```

### 解法

```js
var plusOne = function (digits) {
  const len = digits.length;
  for (let i = len - 1; i >= 0; i--) {
    digits[i]++;
    // 除10取余
    digits[i] %= 10;
    // 不等于0，即没有进位，不再继续遍历，返回数组即可。（499 -> 500）
    if (digits[i] !== 0) return digits;
  }

  // 循环结束还没有返回时，则意味着数组中全部数字都为9。（999 -> 1000）
  digits.shift(1);
  return digits;
};
```
