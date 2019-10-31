---
title: "排序之快速排序"
entitle: quick-sort
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
timestamp: 1572425728
comments: true
date: 2019-10-30 16:55:28
tags:
  - 排序
keywords:
description:
photos:
  - /img/2019/sort.jpg
---

> 希尔排序相当于直接插入排序的升级，同属于插入排序类，堆排序相当于简单选择排序的升级，同属于选择排序类。快速排序是最慢的冒泡排序的升级，属于交换排序类。它是通过不断比较和移动交换来实现排序的，只不过它的实现增大了记录的比较和移动的距离，将关键字较大的记录从前面直接移动到后面，关键字较小的记录从后面直接移动到前面，减少了总的比较次数和移动交换次数。

## 快速排序算法

快速排序（Quick Sort）的基本思想是：通过一趟排序将待排记录分割成独立的两部分，其中一部分记录的关键字均比另一部分记录的关键字小，分别对这两部分记录继续排序，达到整个序列有序。

来直接看下代码：

```java
import java.util.Arrays;

public class QuickSort {

    public static void main(String[] args) {
        int[] arr = {50, 10, 90, 30, 70, 40, 80, 60, 20};
        quickSort(arr, 0, arr.length - 1);
        System.out.println("排序后：" + Arrays.toString(arr));
    }


    public static int partition(int[] arr, int low, int high) {
        // 将数组中第一个元素作为枢轴
        int pivotKey = arr[low];
        // 从数组两端交替向中间扫描
        while (low < high) {
            // 从高端开始，若 arr[high]值大于枢轴值，则向低端循环扫描
            while (pivotKey <= arr[high] && low < high) {
                high--;
            }

            // while 循环结束后，若 low < height ，此时 arr[high] 小于枢轴值，则将 arr[high] 赋值给低端
            if (low < high) {
                arr[low] = arr[high];
                low++;
            }
            // 进行交替，若 arr[low] 小于枢轴值，则向高端循环扫描
            while (pivotKey >= arr[low] && low < high) {
                low++;
            }
            // while 循环结束后，若 low < height ，此时 arr[low] 大于枢轴值，则将 arr[low] 赋值给高端
            if (low < high) {
                arr[high] = arr[low];
                high--;
            }
        }

        // 当 low = high 时，结束循环，此时将枢轴值赋值给该下标，完成本次交换
        arr[low] = pivotKey;

        // 返回枢轴值所在下标
        return low;
    }

    public static void quickSort(int[] arr, int left, int right) {
        if (arr == null || left >= right || arr.length <= 1) {
            return;
        }

        int pivot = partition(arr, left, right);
        quickSort(arr, left, pivot - 1);
        quickSort(arr, pivot + 1, right);
    }

}
```

代码的核心是“int pivot = partition(arr, left, right);”,执行它之前，arr 的数值为 {50,10,90,30,70,40,80,60,20}，partition 函数要做的是先选取当中的一个关键字，如 50，想办法将它放到一个位置，使得左边的值都比它小，右边的值比它大，这样的关键字称为枢轴（pivot）。

经过 partition(arr, 0, 8)的执行之后，数组变成 {20,10,40,30,50,70,80,60,90}，并返回值 4 给 pivot，数字 4 表明 50 放置在数组下标为 4 的位置。计算机把原来的数组变成了两个位于 50 左和右小数组 {20,10,40,30} 和 {70,80,60,90}，而后的递归调用“partition(arr, 0, 4);”和“partition(arr, 5, 8);”，对 {20,10,40,30} 和 {70,80,60,90} 分别进行同样的 partition 操作，直到顺序全部正确为止。

接下来看下关键的 partition 函数执行过程：

```java
public static int partition(int[] arr, int low, int high) {
    // 将数组中第一个元素作为枢轴
    int pivotKey = arr[low];
    // 从数组两端交替向中间扫描
    while (low < high) {
        // 从高端开始，若 arr[high]值大于枢轴值，则向低端循环扫描
        while (pivotKey <= arr[high] && low < high) {
            high--;
        }

        // while 循环结束后，若 low < height ，此时 arr[high] 小于枢轴值，则将 arr[high] 赋值给低端
        if (low < high) {
            arr[low] = arr[high];
            low++;
        }
        // 进行交替，若 arr[low] 小于枢轴值，则向高端循环扫描
        while (pivotKey >= arr[low] && low < high) {
            low++;
        }
        // while 循环结束后，若 low < height ，此时 arr[low] 大于枢轴值，则将 arr[low] 赋值给高端
        if (low < high) {
            arr[high] = arr[low];
            high--;
        }
    }

    // 当 low = high 时，结束循环，此时将枢轴值赋值给该下标，完成本次交换
    arr[low] = pivotKey;

    // 返回枢轴值所在下标
    return low;
}
```

- 当 low = 0，high = arr.length - 1 = 8。pivotKey = 50，先从高端向低端进行扫描，发现 arr[8] < pivotKey，故将 arr[8] 的值赋值给 arr[0]，然后交替从低端向高端进行扫描，发现 arr[2] > pivotKey，故将 arr[2] 的值赋给 arr[high]，即 arr[8] = arr[2] = 90。此时 low = 2，high = 8，序列为 {20, 10, 90, 30, 70, 40, 80, 60, 90}。
- 继续交替从高端向低端进行扫描，发现 arr[5] < pivotKey，故将 arr[5] 的值赋值给 arr[2]，然后交替从低端向高端进行扫描，发现 arr[4] > pivotKey，故将 arr[4] 的值赋给 arr[high]，即 arr[5] = arr[4] = 70。此时 low = 4，high = 5，序列为 {20, 10, 40, 30, 70, 70, 80, 60, 90}。
- 继续交替从高端向低端进行扫描，high-- 到 high = low = 4，此时结束所有循环，arr[4] = pivotKey = 50。此时序列为 {20, 10, 40, 30, 50, 70, 80, 60, 90}，枢轴前的元素全部小于它，枢轴后的元素全部大于它。最后返回枢轴的下标。

之后进行递归调用 “quickSort(arr, 0, 3)” 和 “quickSort(arr, 5, 8)”，对 {20, 10, 40, 30} 和 {70, 80, 60, 90} 分别进行同样的 partition 操作，直到顺序全部正确为止。

## 快速排序复杂度分析

快速排序的时间性能取决于快速排序递归的深度，在最优情况下，即 partition 每次都划分的很均匀，如果排序个关键字，其递归树的深度为 [logn] + 1，即仅需递归 [logn] 次。在最优的情况下，快速排序算法的时间复杂度为 O(nlogn)。在最坏的情况下，待排序的序列为正序或者逆序，每次划分只得到一个比上一次划分少一个记录的子序列，另一个为空。此时时间复杂度为 O(n<sup>2</sup>)。

由于关键字的比较和交换是跳跃执行的，因此，快速排序是一种不稳定的排序方法。

## 快速排序优化

### 优化选取枢轴

- 三数取中（median-of-three）法：取三个关键字先进行排序，将中间数作为枢轴，一般是取左端、右端和中间三个数，也可以随机选取。这样至少这个中间数一定不会是最小或者最大的数。
- 九数取中（me-dian-of-nine）：先从数组中分三次取样，每次取三个数，三个样品各取出中数，然后从这三个中数当中再取出一个中数作为枢轴。更加保证了取到的 pivotkey 是比较接近中间值的关键字。

### 优化小数组时的排序方案

如果数组非常小，其实快读排序反而不如直接插入排序来得更好（直接插入排序时简单排序中性能最好的）。因此我们可以增加一个判断，当 high - low 小于等于常数时用直接插入排序。
