---
title: "[JavaScript LeetCode]53. 最大子序和"
entitle: leetcode-maximum-subarray
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1586964829
comments: true
date: 2020-04-15 23:33:49
tags:
  - LeetCode
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

### 解法一

暴力求解，直接列出所有情况的子数组和进行比较。

```js
var maxSubArray = function (nums) {
  let len = nums.length;
  let maxSum = -Infinity;
  for (let i = 0; i < len; i++) {
    for (let j = i; j < len; j++) {
      let thisSum = 0;
      for (let k = i; k <= j; k++) {
        thisSum += nums[k];
      }

      maxSum = Math.max(thisSum, maxSum);
    }
  }

  return maxSum;
};
```

三层 for 循环，很明显时间复杂度为 O(n<sup>3</sup>)。这种解法在 leetcode 判断会超出时间限制。

### 解法二

对解法一进行优化。在第三层 for 循环计算子数组和时，对于相同的 i, 不同的 j，只要在 j - 1 次循环的基础上累加一项即可。因此，可以将三层 for 循环去掉。时间复杂度为 O(n<sup>2</sup>)

```js
var maxSubArray = function (nums) {
  let len = nums.length;
  let maxSum = nums[0];
  for (let i = 1; i < len; i++) {
    let thisSum = maxSum;
    for (let j = i; j < len; j++) {
      thisSum += nums[j];

      maxSum = Math.max(thisSum, maxSum);
    }
  }

  return maxSum;
};
```

### 解法三

这道题可以用动态规划的思路来解决：

- 首先对数组进行遍历，当前最大连续子序列和为 thisSum，结果为 maxSum
- 如果 thisSum > 0，则说明 thisSum 对结果有增益效果(thisSum + num > num)，则 thisSum 保留并加上当前遍历数字
- 如果 thisSum <= 0，则说明 thisSum 对结果无增益效果(thisSum + num <= num)，需要舍弃，则 thisSum 直接更新为当前遍历数字
- 每次比较 thisSum 和 maxSum 的大小，将最大值置为 maxSum，遍历结束返回结果
- 时间复杂度：O(n)

```js
var maxSubArray = function (nums) {
  let maxSum = nums[0];
  let thisSum = 0;
  for (const num of nums) {
    if (thisSum > 0) {
      thisSum += num;
    } else {
      thisSum = num;
    }
    maxSum = Math.max(sum, maxSum);
  }
  return maxSum;
};
```

### 解法四

看下官方题解，用 Math.max() 方法简化了判断 sum 是否大于 0 的过程，看起来更简洁一些：

```js
var maxSubArray = function (nums) {
  let len = nums.length;
  let maxSum = nums[0];
  let pre = 0;
  nums.forEach(num => {
    pre = Math.max(pre + num, num);
    maxSum = Math.max(pre, maxSum)
  })
  
  return maxSum;
}
```
