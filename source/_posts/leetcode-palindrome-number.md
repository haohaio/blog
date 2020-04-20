---
title: "[JavaScript LeetCode]9. 回文数"
entitle: "leetcode-palindrome-number"
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1545445186
comments: true
date: 2018-12-22 10:19:46
tags:
  - LeetCode
keywords:
description: 回文数
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/palindrome-number/)

判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例:

```code
输入: 121
输出: true

输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。

输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
```

Example 2:

```code
输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
```

进阶:

你能不将整数转为字符串来解决这个问题吗？

### 解法一

根据题意，可以看出负数肯定不是，非 0 的 10 的倍数的数也不是。把数字转为字符串，然后使用数组的 `reverse()` 方法。

```js
var isPalindrome = function (x) {
  if (x < 0 || (x !== 0 && x % 10 === 0)) {
    return false;
  }

  let rev = x.toString().split("").reverse().join("");

  return x == rev;
};
```

### 解法二

把数字进行反转进行比较就可得出结果。

```js
var isPalindrome = function (x) {
  if (x < 0 || (x !== 0 && x % 10 === 0)) {
    return false;
  }

  let copyX = x;
  let rev = 0;
  while (copyX !== 0) {
    rev = rev * 10 + (copyX % 10);
    copyX = parseInt(copyX / 10);
  }

  return x === rev;
};
```

### 解法三

但其实我们不需要将数字完全反转进行比对，只需要将数字反转一半进行然后与前面一半的数字进行比对即可。代码如下：

```js
var isPalindrome = function (x) {
  if (x < 0 || (x !== 0 && x % 10 === 0)) {
    return false;
  }

  let rev = 0;
  while (x > rev) {
    rev = rev * 10 + (x % 10);
    x = parseInt(x / 10);
  }

  return x === rev || x === parseInt(rev / 10);
};
```
