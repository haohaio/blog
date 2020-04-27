---
title: "[JavaScript LeetCode]100. 相同的树"
entitle: leetcode-same-tree
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-04-27 22:37:55
timestamp:
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/same-tree/)

给定两个二叉树，编写一个函数来检验它们是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

示例：

```code
输入:       1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

输出: true

输入:      1          1
          /           \
         2             2

        [1,2],     [1,null,2]

输出: false

输入:       1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

输出: false
```

### 解法

先看一下定义的树结点构造函数：

```js
function TreeNode(val) {
  this.val = val;
  this.left = this.right = null;
}
```

深度优先遍历（先序遍历）。

```js
/**
 * @param {TreeNode} p
 * @param {TreeNode} q
 * @return {boolean}
 */
var isSameTree = function (p, q) {
  // 当两棵树的当前节点都为 null 时返回 true
  if (p === null && q === null) return true;
  // 当其中一个为 null 另一个不为 null 时返回 false
  if (p === null || q === null) return false;
  // 当两个都不为 null 但是值不相等时，返回 false
  if (p.val !== q.val) return false;
  // 当不满足上述终止条件时，继续判断左子树和右子树
  return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
};
```
