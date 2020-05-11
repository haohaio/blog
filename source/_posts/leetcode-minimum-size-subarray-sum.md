---
title: "[JavaScript LeetCode]209. 长度最小的子数组"
entitle: leetcode-minimum-size-subarray-sum
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
comments: true
date: 2020-05-11 15:31:29
timestamp:
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

> 滑动窗口第 4 题

示例：

```code
输入: s = 7, nums = [2,3,1,2,4,3]
输出: 2
解释: 子数组 [4,3] 是该条件下的长度最小的连续子数组。
```

### 滑动窗口解法

```js
var minSubArrayLen = function (s, nums) {
  let left = 0;
  let right = 0;
  let minLen = Number.MAX_SAFE_INTEGER;
  let sum = 0;
  while (right < nums.length) {
    sum += nums[right++];

    while (sum >= s) {
      minLen = Math.min(minLen, right - left);
      sum -= nums[left++];
    }
  }

  return minLen === Number.MAX_SAFE_INTEGER ? 0 : minLen;
};
```
