---
title: '[Java LeetCode]14. Longest Common Prefix'
entitle: leetcode-longest-common-prefix
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 其它
timestamp: 1545787396
comments: true
date: 2018-12-26 09:23:16
tags:
- LeetCode
keywords:
description: Longest Common Prefix
photos:
- /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/longest-common-prefix/)

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string "".

Example 1:

```code
Input: ["flower","flow","flight"]
Output: "fl"
```

Example 2:

```code
Input: ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```

Note:

All given inputs are in lowercase letters a-z.

### 解法一

暴力破解。

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        };

        String prefix = strs[0];
        int len = strs.length;
        for (int i = 1; i < len; i++) {
            // prefix 不是当前项的前缀时
            while (strs[i].indexOf(prefix) != 0) {
                prefix = prefix.substring(0, prefix.length() - 1);

                if (prefix.isEmpty()) {
                    return "";
                }
            }
        }

        return prefix;
    }
}
```

### 解法二

相对于解法一，使用垂直扫描进行优化。并取字符串数组中最短字符串的长度，以减少嵌套遍历次数。

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        };

        int minLen = Integer.MAX_VALUE;
        for (String str : strs) {
            minLen = Math.min(minLen, str.length());
        }

        for (int i = 0; i < minLen; i++){
            char c = strs[0].charAt(i);
            for (int j = 1; j < strs.length; j ++) {
                if (strs[j].charAt(i) != c) {
                    return strs[0].substring(0, i);
                }
            }
        }

        return strs[0].substring(0, minLen);
    }
}
```