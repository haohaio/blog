---
title: "[Java LeetCode]1. Two Sum"
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

[原题链接](https://leetcode.com/problems/two-sum/)

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

### 解法一

暴力解决，循环两次搞定。

```java
public int[] twoSum(int[] nums, int target) {
    int len = nums.length;
    for (int i = 0; i < len; i++) {
        for (int j = i + 1; j < len; j++) {
            if (nums[j] == target - nums[i]) {
                return new int[] { i, j };
            }
        }
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

### 解法二

利用 HashMap 作为存储，key 为当前元素值，value 为当前元素值的索引。遍历时判断 map 中是否存在 key 加上当前元素值可以等于目标值，如果存在，就证明 key 为第一个值，当前元素值为第二个值，各自取其索引即可。

> 维护数组中每个元素到其索引的映射的最佳方法是就是哈希表。

```java
public int[] twoSum(int[] nums, int target) {
        int len = nums.length;
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < len; i++) {
            int complement = target - nums[i];

            if (map.containsKey(complement)) {
                return new int[]{map.get(complement), i};
            }

            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution");
    }
```
