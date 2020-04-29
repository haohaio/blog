---
title: "[JavaScript LeetCode]3. 无重复字符的最长子串"
entitle: leetcode-longest-substring-without-repeating-characters
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
comments: true
date: 2020-04-28 11:05:10
timestamp:
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例：

```code
输入: "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
```

### 暴力解法

我们先枚举所有的子串，然后判断子串内是否有重复字符，更新最长子串的长度值

```js
var lengthOfLongestSubstring = function (s) {
  let len = s.length;
  let longestLen = 0;
  for (let i = 0; i < len; i++) {
    for (let j = 1; j <= len; j++) {
      if (allUnique(s, i, j)) longestLen = Math.max(longestLen, j - i);
    }
  }

  return longestLen;
};

function allUnique(s, start, end) {
  let set = new Set();

  for (let i = start; i < end; i++) {
    if (set.has(s[i])) return false;
    set.add(s[i]);
  }

  return true;
}
```

但是这中解法太慢了，在 LeetCode 上此答案会超出时间限制 - -

### 滑动窗口解法

> 滑动窗口是数组/字符串问题中常用的抽象概念。窗口通常是在数组/字符串中由开始和结束索引定义的一系列元素的集合，即 [i, j)（左闭，右开）。而滑动窗口是可以将两个边界向某一方向“滑动”的窗口。例如，我们将 [i, j) 向右滑动 1 个元素，则它将变为 [i+1, j+1)（左闭，右开）。它可以将嵌套的循环问题，转换为单循环问题，降低时间复杂度。

在暴力法中，我们会反复检查一个子字符串是否含有有重复的字符，但这是没有必要的。如果从索引 i 到 j - 1 之间的子字符串 s<sub>ij</sub> 已经被检查为没有重复字符。我们只需要检查 s[j] 对应的字符是否已经存在于子字符串 s<sub>ij</sub> 中。

接下来看一下滑动窗口解法：

```js
var lengthOfLongestSubstring = function (s) {
  let len = s.length;
  let set = new Set();
  let i = 0;
  let j = 0;
  let ans = 0;
  while (i < len && j < len) {
    if (!set.has(s[j])) {
      set.add(s[j++]);
      ans = Math.max(ans, j - i);
    } else {
      set.delete(s[i++]);
    }
  }

  return ans;
};
```

我们还可以使用数组及其 push 和 shift 方法来模拟滑动窗口：

```js
var lengthOfLongestSubstring = function (s) {
  let ans = 0;
  let temp = [];
  let i = 0;
  while (i < s.length) {
    if (temp.indexOf(s[i]) === -1) {
      temp.push(s[i++]);
      ans = Math.max(ans, temp.length);
    } else {
      temp.shift();
    }
  }

  return ans;
};
```

### 滑动窗口优化

当滑动窗口右边界扩大时发现元素与滑动窗口内元素重复时，其实不需要对左边界一位一位的递增，可以找到与滑动窗口内重复元素的下标，可以直接让左边界等于该下标的后一位。

因此我们需要对下标进行维护。

针对使用数组模拟滑动窗口的方法我们可以这样优化：

```js
var lengthOfLongestSubstring = function (s) {
  let ans = 0;
  let temp = [];
  let i = 0;
  while (i < s.length) {
    let index = temp.indexOf(s[i]);
    if (index === -1) {
      temp.push(s[i++]);
      ans = Math.max(ans, temp.length);
    } else {
      temp.splice(0, index + 1)
    }
  }

  return ans;
};
```

也可以使用 Map 数据结构来对下标进行维护：

```js
var lengthOfLongestSubstring = function (s) {
  let len = s.length;
  let map = new Map();
  let ans = 0;
  for (let i = 0, j = 0; j < len; j++) {
    if (map.has(s[j])) {
      // 例如 'abcaabc'，当移动到第二个 b 时， i = 4，j = 5，map.get('b') = 2，即 map 内重复的 b 不在滑动窗口内，故此时不能直接让 i = map.get(s[j])，需要对边界进行判断
      i = Math.max(i, map.get(s[j]));
    }

    ans = Math.max(ans, j + 1 - i);
    map.set(s[j], j + 1);
  }

  return ans;
};
```

> set 和 map 是用 hash 实现的，故其 has 方法的时间复杂度肯定是优于基于索引的 indexOf 的。
