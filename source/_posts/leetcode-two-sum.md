---
title: "[JavaScript LeetCode]1. 两数之和"
entitle: "leetcode-two-sum"
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1545398651
comments: true
date: 2018-12-21 21:24:11
tags:
  - LeetCode
keywords:
description: Two Sum
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/two-sum/)

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

> 你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例：

```code
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

### 解法一

```js
var twoSum = function (nums, target) {
  for (var i = 0; i < nums.length; i++) {
    var dif = target - nums[i];
    for (var j = i + 1; j < nums.length; j++) {
      if (nums[j] === dif) {
        return [i, j];
      }
    }
  }
};
```

### 解法二

```js
var twoSum = function (nums, target) {
  var temp = {};
  for (var i = 0; i < nums.length; i++) {
    var dif = target - nums[i];
    if (temp[dif] !== undefined) {
      return [temp[dif], i];
    }

    temp[nums[i]] = i;
  }
};
```

```js
var twoSum = function (nums, target) {
  let map = new Map();
  for (var i = 0; i < nums.length; i++) {
    let dif = target - nums[i];

    if (map.has(dif)) {
      return [map.get[i], i];
    }

    map.set(nums[i], i);
  }
};
```
