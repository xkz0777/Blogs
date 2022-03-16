---
title: 数据结构笔记
date: 2021-08-13 12:55:21
tags: 数据结构
author: xkz
categories: 数据结构
summary: 数据结构笔记之概述篇
img: /medias/featureimages/sjjg1.jpg
mathjax: true
---

# 数据结构概述

## 什么是数据结构

数据结构是计算机中存储、组织数据的方式。通常情况下，精心选择的数据结构可以带来最优效率的算法。

## 如何在书架上摆放图书

1. 随便放

	插入 O(1)，查找 O(n)

2. 按照书名的拼音字母顺序排放

	插入 O(n)：例如新进了一本《阿Q正传》将导致后面的书全部需要往后移一位。

	查找 O(logn)：二分查找

3. 把书架按类别划分成几块区域，在每种类别内，按照书名的拼音顺序摆放

## 算法的时间复杂度

分析算法时，通常考虑两种时间复杂度：

+ 最坏时间复杂度 $T_{worst}(n)$
+ 平均时间复杂度 $T_{avg}(n)$

复杂度的表示方式：

+ 大$O$表示法

	$T(n)=O(f(n))$，表示存在常数$C>0,n_0>0$，使得当$n\ge n_0$时有$T(n)\le C\cdot f(n)$，即上界。

+ $\Omega$表示法

	$T(n)=\Omega (g(n))$，表示存在常数$C>0,n_0>0$，使得当$n\ge n_0$时有$T(n)\ge C\cdot g(n)$，即下界。

+ $\Theta$表示法

	$T(n)=\Theta (h(n))$，表示同时有$T(n)=O(h(n))$和$T(n)=\Omega (h(n))$。

## 最大子列和问题

问题：给定 n 个整数的序列，求函数 $f(i,j)=\max\{0,\sum\limits_{k=i}^jA_k\}$ 的最大值

### 算法1：枚举

直接枚举$(i,j)$，$T(n)=O(n^3)$。

### 算法2：前缀和与差分

首先用$O(n)$的时间复杂度算出前缀和，之后枚举$(i,j)$，由于此时枚举后计算序列和是$O(1)$的，因此$T(n)=O(n^2)$

### 算法3：分治

首先把数组分成左右两块，然后递归地求出左右两边的最大子列和，并计算出跨越中线的最大子列和，求这三个数中最大的一个。

求跨越中线的最大子列和的方法：从中线开始，向左扫描，记录最大值，之后向右扫描，再记录最大值，把两个最大值相加即可

代码：

```cpp
#include <iostream>
int maxOf3(int a, int b, int c) {
    return std::max(std::max(a, b), c);
}

int maxSubSum(int* a, int s, int e) {  // a是数组，s是起点，e是终点，左右都是闭区间
    if (s >= e) {
        if (s > e) {
            return 0;
        }
        else {
            return std::max(0, a[s]);
        }
    }
    int m = s + (e - s) / 2;
    int maxLeftSum = 0, maxRightSum = 0;  //分别从m向左、向右扫描得到的最大值
    int sum = 0;                          //作为临时变量
    for (int i = m; i >= s; --i) {
        sum += a[i];
        maxLeftSum = std::max(maxLeftSum, sum);
    }
    sum = 0;
    for (int i = m + 1; i <= e; ++i) {
        sum += a[i];
        maxRightSum = std::max(maxRightSum, sum);
    }
    return maxOf3(maxLeftSum + maxRightSum, maxSubSum(a, s, m), maxSubSum(a, m + 1, e));
}

int main() {
    int n;
    std::cin >> n;
    int* a = new int[n];
    for (int i = 0; i < n; ++i) {
        std::cin >> a[i];
    }
    std::cout << maxSubSum(a, 0, n - 1);
}
```

时间复杂度：可知递推式是

$T(n)=2\cdot T(n/2)+O(n)$

该递推式非常常见（如归并排序），结论为时间复杂度是$T(n)=O(n\cdot\log n)$。

### 算法4：在线处理

在线处理指读取数据时便进行处理，在所有算法中必定是最优的，因为读取时间就是$O(n)$（当然读取时的操作必须是$O(1)$的才能是最快的）。

在本题中，在线处理算法为，从左到右求和，用一个最大值记录，如果遇到更大的便记录，**遇到负数直接丢弃**，因为对于某个最大子列和而言，其不可能出现负值前缀和（否则把该前缀丢弃所得的子列和更大）。

代码：

```cpp
#include <iostream>

int maxSubSum(int* a, int n) {  // a是数组，s是起点，e是终点，左右都是闭区间
    int maxSubSum = 0, sum = 0;
    for (int i = 0; i < n; ++i) {
        sum += a[i];
        sum = std::max(sum, 0);
        maxSubSum = std::max(maxSubSum, sum);
    }
    return maxSubSum;
}

int main() {
    int n;
    std::cin >> n;
    int* a = new int[n];
    for (int i = 0; i < n; ++i) {
        std::cin >> a[i];
    }
    std::cout << maxSubSum(a, n);
}
```

