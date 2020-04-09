---
title: "排序之希尔排序"
entitle: shell-sort
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1571225076
comments: true
date: 2019-10-16 19:24:36
tags:
  - 排序
keywords:
description:
photos:
  - /img/tags/sort.jpg
---

希尔排序（Shell Sort）是 D.L.Shell 与 1959 年提出来的一种排序算法，在这之前排序算法的时间复杂度基本都是 O(n<sup>2</sup>)的，希尔排序算法是突破这个时间复杂度的第一批算法之一。

来直接看下算法：

```java
import java.util.Arrays;

public class ShellSort {

    public static void main(String[] args) {
        int[] arr = {9, 1, 5, 8, 3, 7, 4, 6, 2};

        shellSort(arr);
        for (int i = 0; i < arr.length; i++) {
            System.out.println(arr[i]);
        }

    }


    static void shellSort(int[] arr) {
        // 当前正在比较的数字
        int current;
        // 初始增量（希尔增量）
        int increment = arr.length / 2;

        // increment = 1 的时候，数组已经有序
        while (increment > 0) {
            for (int i = increment; i < arr.length; i++) {
                current = arr[i];
                // 与 current 同组的前一个值
                int preIndex = i - increment;
                // 找到同组内比 current 小的值
                while (preIndex >= 0 && current < arr[preIndex]) {
                    // 将小于 current 的值向前移动 increment 个位置
                    arr[preIndex + increment] = arr[preIndex];
                    preIndex = preIndex - increment;
                }
                // 将 current 插入到到相应的位置
                arr[preIndex + increment] = current;
            }

            // 缩小增量
            increment = increment / 2;
        }
    }

}
```

来看下完整的排序过程：

- do...while 第 1 次循环，increment = 4，for 循环第 1 次, i = 4，current = arr[4] = 3, preIndex = 0，current < arr[0] = 9，进入内层 while 循环，arr[4] = arr[0] = 9，preIndex = -4 < 0，跳出循环。arr[0] = current = 3。此时序列为 {3, 1, 5, 8, 9, 7, 4, 6, 2}。
- for 循环第 2 次，i = 5，current = arr[5] = 7，preIndex = 1，current > arr[1] = 1，不进入内层 while 循环。此时序列仍为 {3, 1, 5, 8, 9, 7, 4, 6, 2}。
- for 循环第 3 次，i = 6，current = arr[6] = 4，preIndex = 2，current < arr[2] = 5，进入内层 while 循环，arr[6] = arr[2] = 5，preIndex = -2，跳出循环。arr[2] = current = 4。此时序列仍为 {3, 1, 4, 8, 9, 7, 5, 6, 2}。
- for 循环第 4 次，i = 7，current = arr[7] = 6，preIndex = 3，current < arr[3] = 8，进入内层 while 循环，arr[7] = arr[3] = 8，preIndex = -1，跳出循环。arr[3] = current = 6。此时序列仍为 {3, 1, 4, 6, 9, 7, 5, 8, 2}。
- for 循环第 5 次，i = 8，current = arr[8] = 2，preIndex = 4，current < arr[4] = 9，进入内层 while 循环，arr[8] = arr[4] = 9，preIndex = 0，current < arr[0] = 3，再次进入 while 循环，arr[4] = arr[0] = 3，preIndex = -4，跳出循环。arr[0] = current = 2。此时序列仍为 {2, 1, 4, 6, 3, 7, 5, 8, 9}。一轮循环结束。
- do...while 第 2 次循环，increment = 2，for 循环第 1 次, i = 2，current = arr[2] = 4, preIndex = 0，current > arr[0] = 2，不进入内层 while 循环。此时序列仍为 {2, 1, 4, 6, 3, 7, 5, 8, 9}。
- for 循环第 2 次, i = 3，current = arr[3] = 6, preIndex = 1，current > arr[1] = 1，不进入内层 while 循环。此时序列仍为 {2, 1, 4, 6, 3, 7, 5, 8, 9}。
- for 循环第 3 次, i = 4，current = arr[4] = 3, preIndex = 2，current < arr[2] = 4，进入内层 while 循环，arr[4] = arr[2] = 4，preIndex = 0，current > arr[0] = 2，跳出循环。arr[2] = current = 3。此时序列为 {2, 1, 3, 6, 4, 7, 5, 8, 9}。
- for 循环第 4 次, i = 5，current = arr[5] = 7, preIndex = 3，current > arr[3] = 6，不进入内层 while 循环。此时序列仍为 {2, 1, 3, 6, 4, 7, 5, 8, 9}。
- for 循环第 5 次, i = 6，current = arr[6] = 5, preIndex = 4，current > arr[4] = 3，不进入内层 while 循环。此时序列仍为 {2, 1, 3, 6, 4, 7, 5, 8, 9}。
- for 循环第 6 次, i = 7，current = arr[7] = 8, preIndex = 5，current > arr[5] = 7，不进入内层 while 循环。此时序列仍为 {2, 1, 3, 6, 4, 7, 5, 8, 9}。
- for 循环第 7 次, i = 8，current = arr[8] = 9, preIndex = 6，current > arr[6] = 5，不进入内层 while 循环。此时序列仍为 {2, 1, 3, 6, 4, 7, 5, 8, 9}。一轮循环结束。
- do...while 第 3 次循环，increment = 3，for 循环第 1 次, i = 1，current = arr[1] = 1, preIndex = 0，current > arr[0] = 2，进入内层 while 循环，arr[1] = arr[0] = 2，preIndex = -1 < 0，跳出循环。arr[0] = current = 1。此时序列仍为 {1, 2, 3, 6, 4, 7, 5, 8, 9}。
- for 循环第 2 次, 此时序列为 {1, 2, 3, 6, 4, 7, 5, 8, 9}。
- for 循环第 3 次, 此时序列为 {1, 2, 3, 6, 4, 7, 5, 8, 9}。
- for 循环第 4 次, 此时序列为 {1, 2, 3, 4, 6, 7, 5, 8, 9}。
- for 循环第 5 次, 此时序列为 {1, 2, 3, 4, 6, 7, 5, 8, 9}。
- for 循环第 6 次, 此时序列为 {1, 2, 3, 4, 5, 6, 7, 8, 9}。
- for 循环第 7 次, 此时序列为 {1, 2, 3, 4, 5, 6, 7, 8, 9}。
- for 循环第 8 次, 此时序列为 {1, 2, 3, 4, 5, 6, 7, 8, 9}。最后一轮循环结束，完成排序。

可以看到希尔排序是将关键字较小的记录不是一步一步地往前挪动，而是跳跃式的往前移，从而使得每次完成一轮循环后，整个序列就朝着有序坚实地迈进一步。这就是希尔排序的关键所在。

## 希尔排序复杂度分析

希尔排序的关键并不是随便分组后各自排序，而是将相隔某个“增量”的记录组成一个子序列，实现跳跃式的移动，使得排序的效率提高。

这里“增量”的选取就十分关键了。可究竟应该选取什么样的增量才是最好，目前还是一个数学难题，迄今为止还没有人找到一种最好的增量序列。不过大量研究表明，其时间复杂度要好于直接排序的 O(n<sup>2</sup>)。

需要注意的是，增量序列的最后一个值必须等于 1 才行。另外由于记录是跳跃式的移动，希尔排序并不是一种稳定的排序。
