---
title: "排序之简单选择排序"
entitle: select-sort
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1571102846
comments: true
date: 2019-10-15 09:27:26
tags:
  - 排序
keywords:
description:
photos:
  - /img/tags/sort.jpg
---

先来看下简单选择排序的基本思想：

> 简单选择排序法（Simple Selection Sort）就是在要排序的一组记录中，选出关键字最小（或者最大）的一个数与第 1 个位置的数交换；然后在剩下的数中再找最小（或者最大）的与第 2 个位置的数交换，以此类推，直到第 n-1 个元素（倒数第二个数）和第 n 个元素（最后一个数）比较为止。

接下来看下代码：

```java
public class SelectSort {

    public static void main(String[] args) {
        int[] arr = {9, 1, 5, 8, 3, 7, 4, 6, 2};

        selectSort(arr);
        for (int i = 0; i < arr.length; i++) {
            System.out.println(arr[i]);
        }
    }


    static void selectSort(int[] arr) {
        int min;
        for (int i = 0; i < arr.length; i++) {
            min = i;

            for (int j = i + 1; j < arr.length; j++) {
                if (arr[min] > arr[j]) {
                    min = j;
                }
            }

            if (i != min) {
                int temp = arr[i];
                arr[i] = arr[min];
                arr[min] = temp;
            }
        }
    }

}
```

可以看到代码并不难理解，针对关键字序列 {9, 1, 5, 8, 3, 7, 4, 6, 2}，当 i = 0 时，我们进行了 8 次计较，但只进行了一次交换。当 i = 2 时，进行 7 次比较，一次交换，之后比较和交换完全雷同，最多完成 8 次交换，就可完成排序工作。

## 简单选择排序复杂度分析

可以看到无论在什么情况下，其比较次数都是一样的多，需要比较 (n - 1) + ... + 3 + 2 + 1 = n \* (n - 1) / 2 次，因此总的时间复杂度为 O(n<sup>2</sup>)。而对于交换次数而言，在最优的情况下，交换 0 次，在最差的情况下，交换 n - 1 次。

尽管时间复杂度与冒泡排序相同，但在最差情况下，交换次数是明显少于冒泡排序的，因此，简单选择排序的性能还是略优于冒泡排序的。
