---
title: "[Java LeetCode]35. Search Insert Position"
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
  - /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/search-insert-position/)

Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

Example 1:

```code
Input: [1,3,5,6], 5
Output: 2
```

Example 2:

```code
Input: [1,3,5,6], 2
Output: 1
```

Example 3:

```code
Input: [1,3,5,6], 7
Output: 4
```

Example 4:

```code
Input: [1,3,5,6], 0
Output: 0
```

### 解法

类似二分查找法

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int startIndex = 0;
        int endIndex = nums.length - 1;

        while (startIndex <= endIndex) {
            int midIndex = (startIndex + endIndex)  / 2;

            if (target <= nums[midIndex]) {
                endIndex = midIndex - 1;
            } else {
                startIndex = midIndex + 1;
            }
        }

        return startIndex;
    }
}
```
