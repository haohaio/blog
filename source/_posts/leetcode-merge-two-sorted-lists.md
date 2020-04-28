---
title: "[JavaScript LeetCode]21. 合并两个有序链表"
entitle: leetcode-merge-two-sorted-lists
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1545916286
comments: true
date: 2018-12-27 21:11:26
tags:
  - LeetCode
keywords:
description: Merge Two Sorted Lists
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

示例:

```code
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

首先要对 ListNode 有一定的了解：

ListNode 存储了两个变量：val 和 next。val 是这个节点的值，next 是指向下一节点的指针，当 next 为空指针时，这个节点是链表的最后一个节点。

```js
function ListNode(val) {
  this.val = val;
  this.next = null;
}
```

### 解法一

可以创建一个新链表，新链表指针每次指向值小的节点，依次比较下去，最后，当其中一个链表到达了末尾，我们只需要把新链表指针指向另一个没有到末尾的链表此时的指针即可。

```js
var mergeTwoLists = function (l1, l2) {
  let head = new ListNode(0);
  let temp = head;

  while (l1 !== null && l2 !== null) {˝
    if (l1.val < l2.val) {
      temp.next = l1;
      l1 = l1.next;
    } else {
      temp.next = l2;
      l2 = l2.next;
    }
    temp = temp.next;
  }

  temp.next = l1 !== null ? l1 : l2;

  return head.next;
};
```

### 解法二

也可以使用递归。

```js
var mergeTwoLists = function (l1, l2) {
  if (l1 === null) return l2;
  if (l2 === null) return l1;

  if (l1.val < l2.val) {
    l1.next = mergeTwoLists(l1.next, l2);
    return l1;
  } else {
    l2.next = mergeTwoLists(l1, l2.next);
    return l2;
  }
};
```
