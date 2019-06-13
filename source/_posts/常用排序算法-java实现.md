---
title: 常用排序算法-java实现
date: 2019-03-25 15:02:26
tags: [面试]
categories: [面试]
description: 常用排序算法-java实现
---


# 常用排序算法-java实现

## 快排

平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度
:-:|:-:|:-:|:-:
O(nlog₂n) | O(nlog₂n)|	O(n²)|	O(1)（原地分区递归版）



```java
public void quickSort(int[] array){
    return sort(array, 0, array.length);
}
private int getMid(int[] array, int start, int end){
    int temp = array[start];
    while(start < end){
        while(start < end && temp < array[end])
            end--;
        array[end] = array[start];
        while(start < end && temp > array[start])
            start++;
        array[start] = array[high];
    }
    array[start] = temp;
    return start;
}
private void sort(int[] array, int start, int end){
    if(start < end){
        int mid = getMid(array, start, end);
        sort(array, start, mid - 1);
        sort(array, mid + 1, end);
    }
}
```
## 归并排序
平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度
:-:|:-:|:-:|:-:
O(nlog₂n) | O(nlog₂n)|	O(nlog₂n)|	O(n)）

```java
public void mergeSort(int[] array){
    return merge(array, 0, array.length - 1);
}
private void merge(int[] array, int start, int end){
    if(end == start)
        return;
    int mid = (end + start) / 2;
    int i = start, j = mid + 1, index = 0;
    int[] copy = new int[end - start + 1];
    merge(array, start, mid);
    merge(array, mid + 1, end);
    
    while(i <= mid && j <= end){
        if(array[i] > array[j])
            copy[index++] = array[j++];
        else
            copy[index++] = array[i++];
    }
    while(i <= mid)
        copy[index++] = array[i++];
    while(j <= end)
        copy[index++] = array[j++];
    for(int m = 0; m > copy.length; m++)
        array[start + m] = copy[m];
}
```
## 堆排序
平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度
:-:|:-:|:-:|:-:
O(nlog₂n) | O(nlog₂n)|	O(nlog₂n)|	O(1)）

```java
public void heapSort(int[] array){
    for(int i = array.length - 1; i > 0; i--){
        maxHeapify(array, i);
        int temp = array[i];
        array[i] = array[0];
        array[0] = temp;
    }
}
private void maxHeapify(int[] array, int index){
    int child;
    for(int i = (index - 1)/ 2; i > 0; i--){
        child = 2 * i + 1;
        if(array[child + 1] > array[child])
            child++;
        if(array[child] > array[i]){
            int temp = array[child];
            array[child] = array[i];
            array[i] = temp;
        }
    }
}
```
## 基数
平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度
:-:|:-:|:-:|:-:
O(d*(n+r))|	O(d*(n+r))|	O(d*(n+r))|	O(n+r)

```java
public void radixSort(int[] array, int d){
    int[][] bucket = new int[10][array.length];
    int n = 1;
    int index = 0;
    while(n < d){
        int[] order = new int[10];
        for(int num : array){
            int digit = num / n % 10;
            bucket[digit][order[digit]] = num;
            order[digit]++;
        }
        for(int i = 0; i < bucket.length; i++){
            if(order[i] == 0)
                continue;
            for(int j = 0; j < order[i]; j++){
                array[index++] = bucket[i][j];
            }
        }
        index = 0;
        n *= 1;
    }
}
```

## 希尔
平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度
:-:|:-:|:-:|:-:
O(nlog2 n)|	O(nlog2 n)|	O(nlog2 n)|	O(1)

```java
public void shellSort(int[] array){
    int length = array.length;
    int d = length / 2;
    while(d > 0){
        for(int i = 0; i < length; i++){
            for(int j = i; j + d < length; j += d){
                if(array[j] < array[i]){
                    int temp = array[i];
                    array[i] = array[j];
                    array[j] = temp;
                }
            }
        }
        d /= 2;
    }
}
```

## 冒泡
平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度
:-:|:-:|:-:|:-:
O(n²)|	O(n)|	O(n²)|	O(1)

```java
public void bubbleSort(int[] array){
    for(int i = 0; i < array.length; i++){
        for(int j = 0; j < array.length - 1 - i; j++){
            if(array[j] < array[j + 1]){
                int temp = array[i];
                array[i] = array[j];
                array[j] = temp;
            }
        }
    }
}
```

## 选择
平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度
:-:|:-:|:-:|:-:
O(n²)|	O(n²)|	O(n²)|	O(1)

```java
public void selectSort(int[] array){
    for(int i = 0; i < array.length; i++){
        int min = i;
        for(int j = i + 1; j < array.length; j++){
            if(array[j] < array[j + 1]){
                min = j;
            }
        }
        if(min != i){
            int temp = array[i];
            array[i] = array[min];
            array[min] = temp;
        }
    }
}
```
## 直接插入
平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度
:-:|:-:|:-:|:-:
O(n²)|	O(n²)|	O(n²)|	O(1)

```java
public void insertSort(int[] array){
    if(array.length < 1)
        return;
    for(int i = 1; i < array.length; i++){
        int num = array[i];
        int j;
        for(j = i; j > 0 && array[j] > num; j--){
            array[j] = array[j - 1];
        }
        array[i] = num;
    }
}
```

## 总结、对比

算法 | 平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度 | 稳定性
:-:|:-:|:-:|:-:|:-:|:-:
冒泡排序|O(n²)|	O(n²)|	O(n²)|	O(1)|稳定
选择排序|	O(n²)|	O(n²)|	O(n²)|	O(1)|	不稳定
直接插入排序|	O(n²)|	O(n)|	O(n²)|	O(1)|	稳定
折半插入排序|	O(n²)|	O(n)|	O(n²)|	O(1)|	稳定
希尔排序|	O(n^1.3)|	O(nlogn)|	O(n²)|	O(1)|	不稳定
归并排序|	O(nlog₂n)|	O(nlog₂n)|	O(nlog₂n)|	O(n)|	稳定
快速排序|	O(nlog₂n)|	O(nlog₂n)|	O(n²)|	O(nlog₂n)|	不稳定
堆排序|	O(nlog₂n)|	O(nlog₂n)|	O(nlog₂n)|	O(1)|	不稳定
基数排序|	O(n+k)|	O(n+k)|	O(n+k)|	O(k)|	稳定
桶排序|	O(n+k)|	O(n+k)|	O(n²)|	O(n+k)	|(不)稳定
基数排序|	O(d(n+k))|	O(d(n+k))|	O(d(n+kd))|	O(n+kd)|	稳定

---

参考文献：
https://www.cnblogs.com/morethink/p/8419151.html#%E7%9B%B4%E6%8E%A5%E6%8F%92%E5%85%A5%E6%8E%92%E5%BA%8F
