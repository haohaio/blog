---
title: "[JavaScript LeetCode]58. 最后一个单词的长度"
entitle: leetcode-length-of-last-word
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1587480431
comments: true
date: 2020-04-21 22:47:11
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

给定一个仅包含大小写字母和空格 ' ' 的字符串 s，返回其最后一个单词的长度。如果字符串从左向右滚动显示，那么最后一个单词就是最后出现的单词。

如果不存在最后一个单词，请返回 0 。

说明：一个单词是指仅由字母组成、不包含任何空格字符的 最大子字符串。

示例:

```code
输入: "Hello World"
输出: 5
```

### 解法一

由于存在字符串末位存在空格的情况，可以使用 `trim()` 方法去除首尾空格，然后找到最后一个空格的下标即可。

```js
var lengthOfLastWord = function (s) {
  s = s.trim();
  return s.length - 1 - s.lastIndexOf(" ");
};
```

### 解法二

不使用 JavaScript 提供的便捷方法，使用纯算法实现。

```js
var lengthOfLastWord = function (s) {
  // 最后一个单词的末位下标
  let end = s.length - 1;
  // 逆序遍历，若字符串末尾又空格，则最后一个单词的末位下标向前移动
  while (end >= 0 && s[end] === " ") end--;
  if (end < 0) return 0;
  // 最后一个单词的首位下标
  let start = end;
  // 逆序遍历，找到空格或到字符串首位为止
  while (start >= 0 && s[start] !== " ") start--;
  return end - start;
};
```
