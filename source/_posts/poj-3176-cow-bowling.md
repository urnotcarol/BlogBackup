---
title: POJ 3176 Cow Bowling 题解
id: 144
date: 2017-02-13 23:35:13
tags:
- 算法
- OJ
categories:
- 算法
- 动态规划
---


[POJ 3176 Cow Bowling](http://poj.org/problem?id=3176)


思路：

用 dp[i][j] 表示，从最顶端到第 [i, j] 个位置的最长路径（用每个点的数字衡量）。

这样可以正向推导。最终得出最后一排每个位置的最长路径，取最大值输出。
```
#include <iostream>
#include <stdio.h>
#include <algorithm>

using namespace std;

const int MAX_N = 350;
int n;
int tri[MAX_N + 3][MAX_N + 3];
int dp[MAX_N + 3][MAX_N + 3];

void solve() {
    int res = 0;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            dp[i][j] = tri[i][j] + max(dp[i - 1][j - 1], dp[i - 1][j]);
        }
    }
    for (int i = 1; i <= n; i++) {
        res = max(res, dp[n][i]);
    }
    printf("%d", res);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            scanf("%d", &tri[i][j]);
        }
    }
    solve();
    return 0;
}
```

&nbsp;