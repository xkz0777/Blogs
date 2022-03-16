---
title: 程序设计II上机：浮点数的幂
date: 2021-07-20 11:25:44
tags: ['C/C++','高精度']
author: xkz
img: /medias/featureimages/sj3.jpg
categories: 'C/C++'
summary: 程序设计II上机题解之浮点数的幂
---

## 浮点数的幂
### 题目描述

给定一个 **正浮点数F** 和一个 **正整数n** ，其中：

1. F **包括小数点固定有“六位”**，它会被写成 `0.1234` 或 `.12300` 或 `00123.` 的形式；**小数点一定存在**，允许前导零、后导零、前导或后导小数点的存在，**数值位不会全零**。
2. n 取值范围为 1 到 10 （含边界）。
   求 F 的 n 次方的精确值。

### 输入描述

输入有若干行（不给出具体数目），每行依次包含：

1. F，占 6 列；
2. 一个空格；
3. n，占两列，右对齐，数值不足两列（1 到 9）用空格补充。

### 输出描述

输出包括若干行，每行包括一个 F 的 n 次方的精确值，但：

1. 前导零和后导零应被清除，如 `012.340` 应以 `12.34` 输出；但 `0.0123` 应以 `.0123` 输出。

2. 后导小数点应被清除，如 `12. ` 应以 `12` 输出；但前导小数点，如 `.12`，必须保留。

**【数据说明】**
你将被测试所有的输入组合，祝你好运。
这里我们强烈建议你使用C的输入输出函数，特别是逐字符输入输出，而非C++的流输入输出。 **你将被分配每个数据点10s的运行时间。**

### 样例输入

```cpp
6.7592  9
98.999 10
00001.  4
.00001  4
```

### 样例输出

```cpp
29448126.764121021618164430206909037173276672
90429072743629540498.107596019456651774561044010001
1
.00000000000000000001
```

### 解题思路

基本的思想是高精度乘法，参见[程序设计简单算法](https://xkz0777.github.io/2021/07/19/simple-algorithm/)这篇博客中的高精度部分。

这里由于是浮点数的乘法，大致思路是先把浮点数的小数点去掉，先整数做乘方，之后再把小数点补回去。

值得注意的是对于前导0、后导0以及小数点的处理：

首先是清除后导小数点，我的办法比较简单，进行一次特判：如果输入就是整数，直接作整数的乘方处理。

其次对于前导0的去除：由于我们的乘法是倒序乘法，只要最后输出的时候，从后往前一直到第一位非0的数开始输出即可。

对于后导0，一定要注意不要多去，例如第四个样例，我们通过高精度乘法得到的字符串为 `00000000000000000001` ，这时该字符串的所有0都要保留，也即这里我们需要先把小数点补上，使之变为 `.00000000000000000001` ，小数点左边部分中的前导0才是可以去除的。

### AC代码

```cpp
#include <cstring>
#include <iostream>
#include <string>
using namespace std;

char s[7];     //初始读入的f
int n;         //幂
int num;       //运算数，这里采取单精*高精的做法
int ans[100];  //操作数

int ston(char *s) {  //把读进来的数转成整数
    int result = 0;
    for (int i = 0; i < 6; ++i) {
        if (s[i] != '.') {
            result *= 10;
            result += s[i] - '0';
        }
    }
    return result;
}

int mul(int num[], int a, int l) {  //num和a相乘，结果存在num里，返回长度
    int i;
    for (i = 0; i < l; ++i) {
        num[i] *= a;
    }
    int highest = 0;
    for (i = 0; i <= l + 10; ++i) {  //注意这里的i不能只加到l+2，因为这里一次可能进很多位
        if (num[i]) {
            highest = i;
        }
        if (num[i] >= 10) {
            num[i + 1] += num[i] / 10;
            num[i] %= 10;
        }
    }
    return highest + 1;
}

int main() {
    while (cin >> s >> n) {
        int pos = 0;            //小数点在从右数第几位
        bool isInteger = true;  //是不是整数
        for (int i = 5; i >= 0; --i, ++pos) {
            if (s[i] == '.') {
                break;
            }
        }
        for (int i = 0; i < pos; ++i) {  //如果小数点右侧有非0的数，说明不是整数
            if (s[5 - i] != '0') {
                isInteger = false;
            }
        }
        if (n == 1) {         //n为1时没有经历乘方过程，没有去前导0，这时也不用转换了，直接原样输出即可
            if (isInteger) {  //是整数，只要去前导0
                bool flag = false;
                for (int i = 0; i < 5 - pos; ++i) {
                    if (s[i] != '0') {
                        flag = true;
                    }
                    if (flag) {
                        cout << s[i];
                    }
                }
                cout << endl;
            }
            else {          //还要去后导0
                int j = 5;  //去后导0
                for (; j >= 0; --j) {
                    if (s[j] != '0') {
                        break;
                    }
                }
                bool flag = false;
                for (int i = 0; i <= j; ++i) {
                    if (s[i] != '0') {
                        flag = true;
                    }
                    if (flag) {
                        cout << s[i];
                    }
                }
                cout << endl;
            }
        }
        else {
            memset(ans, 0, sizeof(ans));
            num = ston(s);
            int j = 0;                     //j是ans的长度
            for (int i = 0; i < 6; ++i) {  //把给的数去掉小数点，倒序转成数组
                if (s[5 - i] != '.') {
                    ans[j++] = s[5 - i] - '0';
                }
            }
            for (int i = 1; i < n; ++i) {
                j = mul(ans, num, j);
            }
            pos *= n;
            if (isInteger) {  //整数，直接输出即可
                for (int i = 0; i < j - pos; ++i) {
                    cout << ans[j - 1 - i];
                }
                cout << endl;
            }
            else {
                if (pos > j) {  //这时要补 pos-j 个 0
                    cout << ".";
                    for (int i = 0; i < pos - j; ++i) {
                        cout << "0";
                    }
                    //下面输出时，去除后导0
                    int i;
                    for (i = 0; ans[i] == 0; ++i)
                        ;
                    for (int k = j - 1; k >= i; --k) {
                        cout << ans[k];
                    }
                    cout << endl;
                }
                else {          //正常输出，在第 j-pos 位补上小数点
                    int n = 0;  //用n从前往后指向第一个非0位
                    for (; n < j && !ans[n]; ++n)
                        ;
                    for (int i = j - 1, k = 0; i >= n; --i, ++k) {
                        if (k == j - pos) {
                            cout << ".";
                        }
                        cout << ans[i];
                    }
                    cout << endl;
                }
            }
        }
    }
}
```



## 吐槽

这个题目其实技术含量不高，但是需要特别**细**，一遍其实很难做对，这里建议找一起上机的同学用 `fc` 对拍。

此外，本次上机有两道题，另一道是个水题：统计字符串出现次数，这里简单介绍一下，并放个代码。

## 统计字符串出现次数

### 题目描述

输出每行出现某字符串的次数 每行最多50个字符。

### 输入描述

输入n（n>=2）行，第一行为要统计的目标字符串，剩下的n-1行为需要对其进行统计的多行字符串.

### 输出描述

输出该字符串在每行出现的次数。

### 样例输入

```
ab
abcaba
abab qekgd aba
```

### 样例输出

```
2
3
```
### 代码
```cpp
#include <cstdio>
#include <cstring>
using namespace std;
int main() {
    char s1[60], s2[60], *p, c;
    //s1是要统计的目标字符串，s2是每行需要对其进行统计的字符串，p作为strstr的返回值
    //c作为getchar()的返回值，当c为EOF表明读入结束
    scanf("%s", s1);
    getchar();
    int n;  //n是最后输出的字串个数
    int l1 = strlen(s1);
    while (1) {
        int l2 = 0;
        while ((c = getchar()) != '\n') {  //每次循环读进一行
            if (c == EOF) {
                return 0;
            }
            s2[l2++] = c;
        }
        n = 0;
        p = strstr(s2, s1);
        while (p) {
            //如果找到，n数目加1，下一次找的起始地址应该是p+l1（因为p是本次地址）
            n++;
            p = strstr(p + l1, s1);
        }
        printf("%d\n", n);
    }
}
```

