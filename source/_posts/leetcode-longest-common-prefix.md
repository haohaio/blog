---
title: "[JavaScript LeetCode]14. 最长公共前缀"
entitle: leetcode-longest-common-prefix
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1545787396
comments: true
date: 2018-12-26 09:23:16
tags:
  - LeetCode
keywords:
description: Longest Common Prefix
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/longest-common-prefix/)

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

示例:

```code
输入: ["flower","flow","flight"]
输出: "fl"

输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
```

说明:

所有输入只包含小写字母 `a-z`。

### 解法一

暴力破解。

```js
var longestCommonPrefix = function (strs) {
  if (!strs.length) return "";

  let prefix = strs[0];
  let len = strs.length;
  for (let i = 1; i < len; i++) {
    while (!strs[i].startsWith(prefix)) {
      prefix = prefix.substring(0, prefix.length - 1);
    }

    if (!prefix.length) {
      return "";
    }
  }

  return prefix;
};
```

### 解法二

相对于解法一，使用垂直扫描进行优化。并取字符串数组中最短字符串的长度，以减少嵌套遍历次数。

```js
var longestCommonPrefix = function (strs) {
  if (!strs.length) return "";

  let minLen = Infinity;
  strs.forEach((str) => {
    minLen = Math.min(minLen, str.length);
  });

  let first = strs[0];
  for (let i = 0; i < minLen; i++) {
    let c = first[i];
    for (let j = 1; j < strs.length; j++) {
      if (strs[j][i] !== c) {
        return first.substring(0, i);
      }
    }
  }

  return first.substring(0, minLen);
};
```

### 解法三

首先利用 `sort` 的排序方法将数组按照编码排序，只需对数组首位和末位进行比较。

```js
var longestCommonPrefix = function (strs) {
  if (!strs.length) return "";

  strs.sort();

  let first = strs[0];
  let end = strs[strs.length - 1];
  if (first === end || end.startsWith(first))) {
    return first;
  }

  for (let i = 0; i < first.length; i++) {
    if (first[i] !== end[i]) {
      return first.substring(0, i);
    }
  }
};
```
