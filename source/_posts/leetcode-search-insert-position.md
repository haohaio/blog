---
title: "[JavaScript LeetCode]35. 搜索插入位置"
entitle: leetcode-search-insert-position
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1547171975
comments: true
date: 2019-01-11 09:59:35
tags:
  - LeetCode
keywords:
description: Search Insert Position
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/search-insert-position/)

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

示例:

```code
输入: [1,3,5,6], 5
输出: 2

输入: [1,3,5,6], 2
输出: 1

输入: [1,3,5,6], 7
输出: 4

输入: [1,3,5,6], 0
输出: 0
```

### 解法一

直接遍历一遍，若目标值小于等于当前下标值，当前下标就是我们需要的值。可以先与首尾元素进行比较，顺便处理目标值比数组所有元素都大的情况。

```js
var searchInsert = function (nums, target) {
  if (target < nums[0]) return 0;
  let len = nums.length;
  if (target > nums[len - 1]) return len;
  for (let i = 0; i < len; i++) {
    if (target <= nums[i]) return i;
  }
};
```

### 解法二

根据题意，我们需要查找有序数组里的元素，那就可以使用二分查找法来解决问题。

```js
var searchInsert = function (nums, target) {
  let left = 0;
  let right = nums.length - 1;

  while (left <= right) {
    let mid = Math.round((left + right) / 2);
    if (nums[mid] === target) {
      return mid;
    } else if (target < nums[mid]) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }

  return left;
};
```
