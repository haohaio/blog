---
title: '[Java LeetCode]26. Remove Duplicates from Sorted Array'
entitle: leetcode-remove-duplicates-from-sorted-array
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: LeetCode
timestamp: 1546048971
comments: true
date: 2018-12-29 10:02:51
tags:
- LeetCode
keywords:
description:
photos:
- /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

Given a sorted array nums, remove the duplicates in-place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.

Example 1:

```code
Given nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.

It doesn't matter what you leave beyond the returned length.
```

Example 2:

```code
Given nums = [0,0,1,1,1,2,2,3,3,4],

Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively.

It doesn't matter what values are set beyond the returned length.
```

Clarification:

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by reference, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

```code
// nums is passed in by reference. (i.e., without making a copy)
int len = removeDuplicates(nums);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

### 解法

用一个 `temp` 变量记录要返回的长度，遍历数组时，如果后面的元素和前面的元素不同，就让 `temp` 变量加 1，最后返回 `temp` 即可。

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int len = nums.length;
        if (len <= 1) {
            return len;
        };

        int temp = 1;
        for (int i = 1; i < len; i++) {
            if (nums[i - 1] != nums[i]) {
                nums[temp] = nums[i];
                temp++;
            }
        }
        return temp;
    }
}
```