---
title: "[JavaScript LeetCode]27. 移除元素"
entitle: leetcode-remove-element
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1546528983
comments: true
date: 2019-01-03 23:23:03
tags:
  - LeetCode
keywords:
description: Remove Element
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/remove-element/)

给你一个数组 nums  和一个值 val，你需要 原地 移除所有数值等于  val  的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

示例:

```code
给定 nums = [3,2,2,3], val = 3,
函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。
你不需要考虑数组中超出新长度后面的元素。

给定 nums = [0,1,2,2,3,0,4,2], val = 2,

函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。
注意这五个元素可为任意顺序。
你不需要考虑数组中超出新长度后面的元素。
```

说明:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

```js
// nums 是以“引用”方式传递的。也就是说，不对实参作任何拷贝
int len = removeElement(nums, val);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

### 解法一

和 26 题一样，并不是真的要删除元素。所以，可以使用 `newLen` 记录要返回数组的长度。遍历数组时，当前元素与目标值不相同时，依次从下标 0 开始，覆盖原始数组的值即可。

```js
var removeElement = function (nums, val) {
  let newLen = 0;
  let len = nums.length;
  for (let i = 0; i < len; i++) {
    if (nums[i] != val) {
      nums[newLen++] = nums[i];
    }
  }

  return newLen;
};
```

### 解法二

当遍历时，遇到等于目标值的元素时，从数组末位元素开始倒序替换当前下标元素，并将要返回的数组长度 - 1。这种解法，当重复值较少时，可以减少赋值操作。

```js
var removeElement = function (nums, val) {
  let len = nums.length;
  for (let i = 0; i < len; ) {
    if (nums[i] === val) {
      nums[i] = nums[len - 1];
      len--;
    } else {
      // 只有当前下标元素和目标值不相等时，下标才移动
      i++;
    }
  }

  return len;
};
```
