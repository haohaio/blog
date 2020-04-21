---
title: "[JavaScript LeetCode]28. 实现 strStr()"
entitle: leetcode-implement-strstr
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1547109759
comments: true
date: 2019-01-10 16:42:39
tags:
  - LeetCode
keywords:
description: Implement strStr()
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/implement-strstr/)

实现 strStr() 函数。

给定一个  haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从 0 开始)。如果不存在，则返回   -1。

示例:

```code
输入: haystack = "hello", needle = "ll"
输出: 2

输入: haystack = "aaaaa", needle = "bba"
输出: -1
```

说明:

当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与 C 语言的 `strstr()` 以及 Java 的 `indexOf()` 定义相符。

### 解法一

根据题意，要实现的 `strStr()` 和 JavaScript 中的 `indexOf()` 是一样的，所以，现成的轮子可以直接拿来使用。

```js
var strStr = function (haystack, needle) {
  return haystack.indexOf(needle);
};
```

### 解法二

遍历即可。

```js
var strStr = function (haystack, needle) {
  let len1 = haystack.length;
  let len2 = needle.length;

  if (len1 < len2) return -1;

  for (let i = 0; ; i++) {
    if (i + len2 > len1) return -1;

    for (let j = 0; ; j++) {
      if (j == len2) {
        return i;
      }

      if (haystack[i + j] !== needle[j]) {
        break;
      }
    }
  }
};
```

### 解法三

使用 `substr` 减少一层遍历。

```js
var strStr = function (haystack, needle) {
  if (needle === "") return 0;

  for (var i = 0; i < haystack.length; i++) {
    if (haystack[i] === needle[0] && haystack.substr(i, needle.length) === needle) return i;
  }
  return -1;
};
```
