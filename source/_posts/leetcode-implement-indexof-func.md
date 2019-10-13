---
title: "[Java LeetCode]28. Implement strStr()"
entitle: leetcode-implement-indexof-func
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1547109759
comments: true
date: 2019-01-10 16:42:39
tags:
  - LeetCode
keywords:
description: Implement strStr()
photos:
  - /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/implement-strstr/)

Implement [strStr()](http://www.cplusplus.com/reference/cstring/strstr/).

Return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

Example 1:

```code
Input: haystack = "hello", needle = "ll"
Output: 2
```

Example 2:

```code
Input: haystack = "aaaaa", needle = "bba"
Output: -1
```

Clarification:

What should we return when needle is an empty string? This is a great question to ask during an interview.

For the purpose of this problem, we will return 0 when needle is an empty string. This is consistent to C's [strStr()](http://www.cplusplus.com/reference/cstring/strstr/) and Java's [indexOf()](<https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#indexOf(java.lang.String)>).

### 解法

遍历即可。

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int len1 = haystack.length();
        int len2 = needle.length();

        if (len1 < len2) {
            return -1;
        }

        for (int i = 0; ; i++) {
            if (i + len2 > len1) {
                return -1;
            }

            for (int j = 0; ; j++) {
                // 当相同的字符串长度等于子串的长度时，返回主串的长度即可
                if (j == len2) {
                    return i;
                }

                if (haystack.charAt(i + j) != needle.charAt(j)) {
                    break;
                }
            }
        }
    }
}
```
