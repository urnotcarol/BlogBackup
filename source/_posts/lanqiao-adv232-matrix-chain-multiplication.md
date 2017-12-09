---
title: 蓝桥杯 ADV232 矩阵乘法 题解 区间DP
id: 324
date: 2017-04-05 11:13:29
tags: 动态规划 区间DP
---

### 题目
有n个矩阵，大小分别为`a0*a1`, `a1*a2`, `a2*a3`, ..., `a[n-1]*a[n]`，现要将它们依次相乘，只能使用结合率，求最少需要多少次运算。

两个大小分别为`p*q`和`q*r`的矩阵相乘时的运算次数计为`p*q*r`。

<!-- more -->

### 输入
输入的第一行包含一个整数n，表示矩阵的个数。第二行包含n+1个数，表示给定的矩阵。
> 3
>
> 1 10 5 20

* 数据规模和约定

	1<=n<=1000, 1<=ai<=10000

### 思路
经典的石子合并问题，《算法导论》动态规划这一章有非非非常详细的讲解。以前没有做过这种 DP 题。

代码有三层循环，第一层 *l* 表示合并的堆数，从 2 到 n，代表从两堆到三堆到四堆...逐渐推出合并所有需要的代价。

第二层 *i* 表示合并的起始序号，相应的 *j* 就表示结束序号。

第三层 *k* 表示从 *i* 遍历到 *j*，找出最优的分割点。


<!-- more -->
### 代码
```
#include <iostream>
#include <stdio.h>
#include <limits.h>
#define ONLINE_JUDGE

using namespace std;

const int MAX_N = 1000 + 16;
const long long INF = LLONG_MAX;  //LLONG_MAX 定义在头文件 <limits.h> 中
int n;
long long dp[MAX_N][MAX_N];
long long p[MAX_N];

int main() {
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif
    scanf("%d", &n);
    for (int i = 0; i <= n; i++) {
        scanf("%lld", &p[i]);
    }
    for (int i = 1; i <= n; i++) {
        dp[i][i] = 0;
    }
    for (int l = 2; l <= n; l++) {
        for (int i = 1; i <= n - l + 1; i++) {
            int j = i + l - 1;
            dp[i][j] = INF;
            for (int k = i; k < j; k++) {
                dp[i][j] = min(dp[i][j], dp[i][k] + dp[k + 1][j] + p[i - 1] * p[k] * p[j]);
            }
        }
    }
    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    printf("%lld\n", dp[1][n]);
    return 0;

}
```
### 知识水平
头文件`<limits.h>`是个好文件，里面包含了各种变量的范围，当不需要对最大值最小值进行相加操作时（不会溢出），可以用这个里面的定义。