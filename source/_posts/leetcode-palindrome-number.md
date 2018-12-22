---
title: '[Java LeetCode]9. Palindrome Number'
entitle: 'leetcode-palindrome-number'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 其它
timestamp: 1545445186
comments: true
date: 2018-12-22 10:19:46
tags:
- LeetCode
keywords:
description: Palindrome Number
photos:
- /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/reverse-integer/)

Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

Example 1:

```code
Input: 121
Output: true
```

Example 2:

```code
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

Example 3:

```code
Input: 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```

Follow up:

Coud you solve it without converting the integer to a string?

### 解法一

题意是判断一个有符号整型数是否是回文，即逆序过来的整数和原整数相同。则负数肯定不是，非 0 的 10 的倍数的数也不是。那就把数字进行反转进行比较就可得出结果。代码如下：

```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x < 0 || (x != 0 && x % 10 == 0)) {
            return false;
        }

        int copyX = x;
        int revertedNumber = 0;
        while (copyX != 0) {
            revertedNumber = revertedNumber * 10 + copyX % 10;
            copyX /= 10;
        }
        return x == reverse;
    }
}
```

### 解法2

但其实我们不需要将数字完全反转进行比对，只需要将数字反转一半进行然后与前面一半的数字进行比对即可。代码如下：

```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x < 0 || (x != 0 && x % 10 == 0)) {
            return false;
        }

        int revertedNumber = 0;
        while (x > revertedNumber) {
            revertedNumber = revertedNumber * 10 + x % 10;
            x /= 10;
        }
        return x == revertedNumber || x == revertedNumber / 10;
    }
}
```