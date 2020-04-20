---
title: "[Java LeetCode]13. 罗马数字转整数"
entitle: leetcode-roman-to-integer
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1545615646
comments: true
date: 2018-12-24 09:40:46
tags:
  - LeetCode
keywords:
description: Roman to Integer
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/roman-to-integer/)

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

```code
字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

例如， 罗马数字 2 写做  II ，即为两个并列的 1。12 写做  XII ，即为  X + II 。 27 写做   XXVII, 即为  XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做  IIII，而是  IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为  IX。这个特殊的规则只适用于以下六种情况：

- I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
- X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。
- C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。

给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

示例:

```code
输入: "III"
输出: 3

输入: "IV"
输出: 4

输入: "IX"
输出: 9

输入: "LVIII"
输出: 58
解释: L = 50, V= 5, III = 3.

输入: "MCMXCIV"
输出: 1994
解释: M = 1000, CM = 900, XC = 90, IV = 4.
```

### 解法

可以看到罗马数字的计数机制中，小的数字（限于 Ⅰ、X 和 C）在大的数字的左边，所表示的数等于大数减小数得到的数。其它情况都是数字相加即可。那么我们可以利用 HashMap 来完成罗马数字的 7 个数字符号与整数的映射关系，然后对字符串逆向遍历，处理得到结果。代码如下：

```js
var romanToInt = function (s) {
  var hashNum = {
    I: 1,
    V: 5,
    X: 10,
    L: 50,
    C: 100,
    D: 500,
    M: 1000,
  };

  var len = s.length;
  var result = hashNum[s[len - 1]];
  for (var i = len - 2; i >= 0; i--) {
    if (hashNum[s[i]] < hashNum[s[i + 1]]) {
      result -= hashNum[s[i]];
    } else {
      result += hashNum[s[i]];
    }
  }

  return result;
};
```
