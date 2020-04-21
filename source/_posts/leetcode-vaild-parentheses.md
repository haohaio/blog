---
title: "[JavaScript LeetCode]20. 有效的括号"
entitle: leetcode-vaild-parentheses
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1545834750
comments: true
date: 2018-12-26 22:32:30
tags:
  - LeetCode
keywords:
description: Vaild Parentheses
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode.com/problems/valid-parentheses/)

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

示例:

```code
输入: "()"
输出: true

输入: "()[]{}"
输出: true

输入: "(]"
输出: false

输入: "([)]"
输出: false

输入: "{[]}"
输出: true
```

### 解法一

通过观察发现，在合法的情况下，当遇到第一个右括号时，其前面紧邻的一个括号必定是和其匹配的左括号。那么我们可以用栈来解决这个问题，当出现左括号的时候入栈，当遇到右括号时，判断栈顶的左括号是否和其匹配，匹配的话出栈，不匹配的话直接返回 false 即可。最终判断是否空栈即可。在 JavaScript 中，我们可以使用数组模拟栈数据结构。

```js
var isValid = function (s) {
  const map = {
    ")": "(",
    "]": "[",
    "}": "{",
  };

  let stack = [];
  for (let i = 0; i < s.length; i++) {
    let c = s[i];
    if (map[c] !== undefined) {
      let topElement = stack.length ? stack.pop() : "";
      if (topElement !== map[c]) {
        return false;
      }
    } else {
      stack.push(c);
    }
  }

  return stack.length === 0;
};
```

### 解法二

用数字的 `++` 和 `--`模拟栈的操作，因为无需删除数组元素使其操作更快。有个细节注意下 top = 1;，从而省去了之后判空的操作和 top - 1 导致数组越界的错误。

```js
var isValid = function (s) {
  const map = {
    ")": "(",
    "]": "[",
    "}": "{",
  };

  let top = 1;
  let stack = new Array(s.length + top);
  for (let i = 0; i < s.length; i++) {
    let c = s[i];
    if (map[c] !== undefined) {
      let topElement = stack[--top];
      if (topElement !== map[c]) {
        return false;
      }
    } else {
      stack[top++] = c;
    }
  }

  return top === 1;
};
```

### 解法三

找到最内层的括号对，消去，重复此过程，若存在无法消去的字符则说明字符串无效。逻辑简单，但是效率不高。

```js
var isValid = function (s) {
  while (s.length) {
    let temp = s;
    s = s.replace(/\(\)/g, "");
    s = s.replace(/\[\]/g, "");
    s = s.replace(/\{\}/g, "");
    if (s === temp) return false;
  }

  return true;
};
```
