---
title: '[JavaScript LeetCode]53. 最大子序和'
entitle: leetcode-maximum-subarray
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1586964829
comments: true
date: 2020-04-15 23:33:49
tags:  LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/maximum-subarray/)

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

> 你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例：

```code
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

### 解法

这道题可以用动态规划的思路来解决：

- 首先对数组进行遍历，当前最大连续子序列和为 sum，结果为 ans
- 如果 sum > 0，则说明 sum 对结果有增益效果(sum + num > num)，则 sum 保留并加上当前遍历数字
- 如果 sum <= 0，则说明 sum 对结果无增益效果(sum + num <= num)，需要舍弃，则 sum 直接更新为当前遍历数字
- 每次比较 sum 和 ans 的大小，将最大值置为 ans，遍历结束返回结果
- 时间复杂度：O(n)

```js
var maxSubArray = function(nums) {
    let ans = nums[0];
    let sum = 0;
    for(const num of nums) {
        if(sum > 0) {
            sum += num;
        } else {
            sum = num;
        }
        ans = Math.max(ans, sum);
    }
    return ans;
};
```
