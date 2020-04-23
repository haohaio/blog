---
title: "[JavaScript LeetCode]70. 爬楼梯"
entitle: leetcode-climbing-stairs
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-04-23 23:12:17
timestamp:
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例:

```code
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶

输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```

### 解法一

本题可以采用动态规划的思想，可以将问题分成多个子问题，爬第 n 阶楼梯的方法数量，等于 2 部分之和：

1. 爬上 `n-1` 阶楼梯的方法数量。因为再爬 1 阶就能到第 n 阶
2. 爬上 `n-2` 阶楼梯的方法数量。因为再爬 2 阶就能到第 n 阶

所以我们得到公式 `dp[n] = dp[n-1] + dp[n-2]`。

同时需要初始化 `dp[1]=1` 和 `dp[2]=2`。（由于 n 是正整数，故从 `dp[1]`初始化）

时间复杂度：O(n)。

```js
var climbStairs = function (n) {
  const dp = [];
  dp[1] = 1;
  dp[2] = 2;
  for (let i = 3; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
  }

  return dp[n];
};
```

### 解法二

不使用数组，对空间复杂度进行优化。但使用这种方法时，考虑到 n 为 1 或者 2 的情况，故从 dp[0] 开始初始化。

```js
var climbStairs = function (n) {
  let preOfPre = 1; // 相当于 dp[0]
  let pre = 1; // 相当于 dp[1]
  let res = 1;
  for (let i = 2; i <= n; i++) {
    res = pre + preOfPre;
    preOfPre = pre;
    pre = res;
  }

  return res;
};
```
