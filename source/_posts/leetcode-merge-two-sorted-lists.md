---
title: '[Java LeetCode]21. Merge Two Sorted Lists'
entitle: leetcode-merge-two-sorted-lists
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories:  其它
timestamp: 1545916286
comments: true
date: 2018-12-27 21:11:26
tags:
- LeetCode
keywords:
description:
photos:
- /img/2018/leetcode.png
---

[原题链接](https://leetcode.com/problems/merge-two-sorted-lists/)

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Example:

```code
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```

### 解法

首先要对 ListNode 有一定的了解：

ListNode 存储了两个变量：val 和 next。val 是这个节点的值，next 是指向下一节点的指针，当 next 为空指针时，这个节点是链表的最后一个节点。

```java
class ListNode {
    int val; // 当前指针的值
    ListNode next; // 下一个节点

    ListNode(int x){
        val = x;
        next = null;
    }
}
```

新链表指针每次指向值小的节点，依次比较下去，最后，当其中一个链表到达了末尾，我们只需要把新链表指针指向另一个没有到末尾的链表此时的指针即可。

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode temp = head;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                temp.next = l1;
                l1 = l1.next;
            } else {
                temp.next = l2;
                l2 = l2.next;
            }
            temp = temp.next;
        }
        temp.next = l1 != null ? l1 : l2;
        return head.next;
    }
}
```
