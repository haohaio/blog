---
title: '[Java LeetCode]7. Reverse Integer'
entitle: 'leetcode-reverse-integer'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 其它
timestamp: 1545404912
comments: true
date: 2018-12-21 23:08:32
tags:
- LeetCode
keywords:
description: Reverse Integer
photos:
- /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/reverse-integer/)

Given a 32-bit signed integer, reverse digits of an integer.

Example 1:

```code
Input: 123
Output: 321
```

Example 2:

```code
Input: -123
Output: -321
```

Example 3:

```code
Input: 120
Output: 21
```

Note:
> Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−2^31,  2^31 − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

### 解法：

- 对数字进行反转的方法如下

```java
int pop = x % 10;
x /= 10;

int rev = 0;
rev = rev * 10 + pop;
```

- 在32位的机器上，Integer 的取值范围为 [-2^31, 2^31 -1], 即 [-2147483648, 2147483647]。所以最后反转的结果有可能会超出取值范围，需要进行特殊处理。

代码如下：

```java
class Solution {
    public int reverse(int x) {
        int rev = 0;
        while (x != 0) {
            int pop = x % 10;
            x /= 10;

            // Integer.MAX_VALUE = 2147483647
            if (rev > Integer.MAX_VALUE / 10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) {
              return 0;
            };

            // Integer.MIN_VALUE = -2147483648
            if (rev < Integer.MIN_VALUE / 10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) {
              return 0;
            };

            rev = rev * 10 + pop;
        }
        return rev;
    }
}
```