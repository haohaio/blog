---
title: '[Java LeetCode]13. Roman to Integer'
entitle: leetcode-roman-to-integer
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 其它
timestamp: 1545615646
comments: true
date: 2018-12-24 09:40:46
tags:
- LeetCode
keywords:
description: Roman to Integer
photos:
- /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/roman-to-integer/)

Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.

| Symbol | Value |
| :----: | :---: |
|   I    |   1   |
|   V    |   5   |
|   X    |  10   |
|   L    |  50   |
|   C    |  100  |
|   D    |  500  |
|   M    | 1000  |

For example, two is written as II in Roman numeral, just two one's added together. Twelve is written as, XII, which is simply X + II. The number twenty seven is written as XXVII, which is XX + V + II.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not IIII. Instead, the number four is written as IV. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as IX. There are six instances where subtraction is used:

- I can be placed before V (5) and X (10) to make 4 and 9.
- X can be placed before L (50) and C (100) to make 40 and 90.
- C can be placed before D (500) and M (1000) to make 400 and 900.

Given a roman numeral, convert it to an integer. Input is guaranteed to be within the range from 1 to 3999.

Example 1:

```code
Input: "III"
Output: 3
```

Example 2:

```code
Input: "IV"
Output: 4
```

Example 3:

```code
Input: "IX"
Output: 9
```

Example 4:

```code
Input: "LVIII"
Output: 58
Explanation: L = 50, V = 30 and III = 3.
```

Example 5:

```code
Input: "MCMXCIV"
Output: 1994
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

### 解法

可以看到罗马数字的计数机制中，小的数字（限于 Ⅰ、X 和 C）在大的数字的左边，所表示的数等于大数减小数得到的数。其它情况都是数字相加即可。那么我们可以利用 HashMap 来完成罗马数字的 7 个数字符号与整数的映射关系，然后对字符串逆向遍历，处理得到结果。代码如下：

```java
class Solution {
    public int romanToInt(String s) {
        HashMap<Character, Integer> map = new HashMap<>();
        map.put('I', 1);
        map.put('V', 5);
        map.put('X', 10);
        map.put('L', 50);
        map.put('C', 100);
        map.put('D', 500);
        map.put('M', 1000);
        int len = s.length();
        int sum = map.get(s.charAt(len - 1));
        for (int i = len - 2; i >= 0; i--) {
            if (map.get(s.charAt(i)) < map.get(s.charAt(i + 1))) {
                sum -= map.get(s.charAt(i));
            } else {
                sum += map.get(s.charAt(i));
            }
        }
        return sum;
    }
}
```