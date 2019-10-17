---
title: '排序之直接插入排序'
entitle: insert-sort
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1571127274
comments: true
date: 2019-10-15 16:14:34
tags:
keywords:
description:
photos:
---

先来看下直接插入排序的基本思想：

> 直接插入排序（Straight Insertion Sort）的基本操作就是将 n 个待排序的元素看成一个有序表和一个无序表，开始的时候有序表只有 1 个元素，无序表中有 n-1 个元素。每次从无序表中取出第一个元素，将它插入到有序表中，使之成为新的有序表，重复 n-1 次完成整个排序过程。

接下来看下代码：

```java
import java.util.Arrays;

public class InsertSort {

    public static void main(String[] args) {
        int[] arr = {5, 3, 4, 6, 2};

        insertSort(arr);
        for (int i = 0; i < arr.length; i++) {
            System.out.println(arr[i]);
        }

    }

    static void insertSort(int[] arr) {
        int j = 0;
        int temp = 0;

        // 从第二个数开始比较
        for (int i = 1; i < arr.length; i++) {
            // 将当前数插入到已经有序的数组中
            temp = arr[i];

            // 对下标为 i 的之前的元素进行遍历
            for (j = i - 1; j >= 0; j--) {
                // 如果前面的数大于当前数，将他后移
                if (arr[j] > temp) {
                    arr[j + 1] = arr[j];
                } else {
                    break;
                }
            }
            // 将当前轮数的数放到应该在的位置
            arr[j + 1] = temp;

            System.out.println("j === " + j);
            System.out.println("temp === " + temp);
            System.out.println(Arrays.toString(arr));
        }
    }

}
```

具体排序过程如下：

- 当 i = 1 时，temp = arr[1] = 3，即有序表为{3}, 无序表为 {1, 5, 6, 2}, 然后遍历 arr[1] 之前的元素，即 {5}，只有一个元素 arr[0]，并且比 temp 大，则将该元素向后移，即 arr[1] = arr[0] = 5，遍历结束后，j = -1，arr[-1 + 1] = temp = 3。此时序列为 {3, 5, 4, 6, 2}，有序表为{3, 5}，无序表为 {4, 6, 2}。
- 当 i = 2 时，temp = arr[2] = 4，然后遍历 arr[2] 之前的元素，即 {3, 5}，发现 arr[1] 比 temp 大，则将该元素向后移，即，arr[2] = arr[1] = 5，遍历结束后，j = 0，arr[0 + 1] = temp = 4。此时序列为 {3, 4, 5, 6, 2}，有序表为{3, 4, 5}，无序表为 {6, 2}。
- 当 i = 3 时，temp = arr[3] = 6，然后遍历 arr[3] 之前的元素，即 {3, 4, 5}，发现没有元素比 temp 大，遍历结束后，j = 2，arr[2 + 1] = temp = 6。此时序列为 {3, 4, 5, 6, 2}，有序表为{3, 4, 5, 6}，无序表为 {2}。
- 当 i = 4 时，temp = arr[4] = 2，然后遍历 arr[4] 之前的元素，即 {3, 4, 5, 6}，发现所有元素都比 temp 大，则将所有元素都向后移，遍历结束后，j = -1，arr[-1 + 1] = temp = 2。此时序列为 {2，3, 4, 5, 6}，排序完成。

## 直接插入排序复杂度分析

当最快的情况下，即待排序表时逆序的情况，比如 {6, 5, 4, 3, 2}，此时需要比较 2 + 3 + ... + n = (n + 2)(n - 1) / 2 次。时间复杂度还是 O(n<sup>2</sup>)。

显然，直接插入排序在记录本身就是基本有序的时候相对冒泡和简单选择排序而言是非常高效的，此时我们只需要少量的插入操作，就可以完成整个排序操作。
