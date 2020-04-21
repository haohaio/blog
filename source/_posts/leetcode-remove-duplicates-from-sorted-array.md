---
title: "[JavaScript LeetCode]26. 删除排序数组中的重复项"
entitle: leetcode-remove-duplicates-from-sorted-array
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1546048971
comments: true
date: 2018-12-29 10:02:51
tags:
  - LeetCode
keywords:
description: Remove Duplicates from Sorted Array
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

示例:

```code
给定数组 nums = [1,1,2],
函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。
你不需要考虑数组中超出新长度后面的元素。

给定 nums = [0,0,1,1,1,2,2,3,3,4],
函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。
你不需要考虑数组中超出新长度后面的元素。
```

说明:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

```js
// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
int len = removeDuplicates(nums);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

### 解法

根据题意，并不是真的要删除数组中重复的元素。 所以可以用一个 `temp` 变量记录要返回的长度，遍历数组时，如果后面的元素和前面的元素不同，就以 `temp` 值为下标，修改该下标对应的值，然后让 `temp` 变量加 1，最后返回 `temp` 即可。

```js
var removeDuplicates = function (nums) {
  let len = nums.length;
  if (len <= 1) return len;

  let temp = 1;
  for (let i = 1; i < len; i++) {
    if (nums[i - 1] !== nums[i]) {
      nums[temp++] = nums[i];
    }
  }

  return temp;
};
```
