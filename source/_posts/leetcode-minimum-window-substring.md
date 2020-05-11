---
title: "[JavaScript LeetCode]76. 最小覆盖子串"
entitle: leetcode-minimum-window-substring
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
comments: true
date: 2020-05-11 10:53:25
timestamp:
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/minimum-window-substring/)

> 滑动窗口第 3 题

给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字符的最小子串。

示例：

```code
输入: S = "ADOBECODEBANC", T = "ABC"
输出: "BANC"
```

说明：

- 如果 S 中不存这样的子串，则返回空字符串 ""。
- 如果 S 中存在这样的子串，我们保证它是唯一的答案。

### 滑动窗口解法

解题思路与 30 题基本一致。

```js
var minWindow = function (s, t) {
  let left = 0;
  let right = 0;

  let needs = {};
  let windows = {};
  let match = 0;
  let start = 0;
  let minLen = Number.MAX_SAFE_INTEGER;

  for (let c of t) {
    needs[c] ? needs[c]++ : (needs[c] = 1);
  }

  let needsKeyLen = Object.keys(needs).length;

  while (right < s.length) {
    let c1 = s[right];
    if (needs[c1]) {
      windows[c1] ? windows[c1]++ : (windows[c1] = 1);

      if (windows[c1] === needs[c1]) match++;
    }

    right++;
    while (match === needsKeyLen) {
      if (right - left < minLen) {
        minLen = right - left;
        start = left;
      }

      let c2 = s[left];
      if (needs[c2]) {
        windows[c2]--;
        if (windows[c2] < needs[c2]) {
          match--;
        }
      }

      left++;
    }
  }

  return minLen === Number.MAX_SAFE_INTEGER ? "" : s.substr(start, minLen);
};
```
