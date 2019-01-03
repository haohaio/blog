---
title: '[Java LeetCode]20. Vaild Parentheses'
entitle: leetcode-vaild-parentheses
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: LeetCode
timestamp: 1545834750
comments: true
date: 2018-12-26 22:32:30
tags:
- LeetCode
keywords:
description: Vaild Parentheses
photos:
- /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/valid-parentheses/)

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

Example 1:

```code
Input: "()"
Output: true
```

Example 2:

```code
Input: "()[]{}"
Output: true
```

Example 3:

```code
Input: "(]"
Output: false
```

Example 4:

```code
Input: "([)]"
Output: false
```

Example 5:

```code
Input: "{[]}"
Output: true
```

### 解法一

通过观察发现，在合法的情况下，当遇到第一个右括号时，其前面紧邻的一个括号必定是和其匹配的左括号。那么我们可以用栈来解决这个问题，当出现左括号的时候入栈，当遇到右括号时，判断栈顶的左括号是否和其匹配，匹配的话出栈，不匹配的话直接返回 false 即可。最终判断是否空栈即可。

```java
class Solution {
    public boolean isValid(String s) {
        HashMap<Character, Character> map = new HashMap<Character, Character>();
        map.put(')', '(');
        map.put(']', '[');
        map.put('}', '{');

        Stack<Character> stack = new Stack<Character>();

        for (int i = 0; i < s.length(); i++) {
          char c = s.charAt(i);

          if (map.containsKey(c)) {
            char topElement = stack.empty() ? '#' : stack.pop();

            if (topElement != map.get(c)) {
              return false;
            }
          } else {
            stack.push(c);
          }
        }

        return stack.isEmpty();
    }
}
```

### 解法二

用数字的 `++` 和 `--`模拟栈的操作使其操作更快。有个细节注意下 top = 1;，从而省去了之后判空的操作和 top - 1 导致数组越界的错误。

```java
class Solution {
    public boolean isValid(String s) {
        // HashMap<Character, Character> map = new HashMap<Character, Character>();
        // map.put(')', '(');
        // map.put(']', '[');
        // map.put('}', '{');
        int top = 1;
        char[] stack = new char[s.length() + top];
        for (char c : s.toCharArray()) {
            // if (map.containsKey(c)) {
            //     if (stack[--top] != map.get(c)) {
            //         return false;
            //     }
            // } else {
            //     stack[top++] = c;
            // }

            if (c == '(' || c == '[' || c == '{') {
                stack[top++] = c;
            } else if (c == ')' && stack[--top] != '(') {
                return false;
            } else if (c == ']' && stack[--top] != '[') {
                return false;
            } else if (c == '}' && stack[--top] != '{') {
                return false;
            }
        }
        return top == 1;
    }
}
```