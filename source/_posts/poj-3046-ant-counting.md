---
title: POJ 3046 Ant Counting 题解
id: 157
date: 2017-03-08 11:32:37
tags:
---


[POJ 3046 Ant Counting](http://poj.org/problem?id=3046)


## 题目大意

有 T 种类型的蚂蚁，每种的数量各异，一共 A 只。不同类型的蚂蚁可以区分而同种的不可以。问用这些蚂蚁组成数量为 S~B 之间的小队，一共有多少种组合方法。比如 S 为 2，B为3，分别计算组成 2 人队和 3 人队有多少种组合方法，相加就是答案。

## 思路

多重背包求方案总数问题。

在这道题里面学到了二维滚动数组，把 dp[MAX_T][MAX_A] 压缩成为 dp[2][MAX_A]. 用与 1 运算 &amp; 来区分当前和前一个数组。


<!-- more -->
## 代码
```
#include <iostream>
#include <algorithm>
#include <memory.h>
#include <stdio.h>

using namespace std;

const int MAX_T = 1000;
const int MAX_A = 100000;
const int MOD = 1000000;
int T;
int A;
int S;
int B;
int c[MAX_T + 3];
int dp[2][MAX_A + 3];

int main() {
    scanf("%d %d %d %d", &amp;T, &amp;A, &amp;S, &amp;B);
    int ant;
    for (int i = 0; i &lt; A; i++) {
        scanf("%d", &amp;ant);
        c[ant]++;
    }

    dp[0][0] = 1;
    for (int i = 1; i &lt;= T; i++) {
        int cur = i &amp; 0x1;
        int pre = (i - 1) &amp; 0x1;
        memset(dp[cur], 0, sizeof(dp[cur]));
        for (int j = 0; j &lt;= A; j++) {
            for (int k = 0; k &lt;= min(c[i], j); k++) {
                dp[cur][j] = (dp[cur][j] + dp[pre][j - k]) % MOD;
            }
        }
    }

    int res = 0;
    for (int i = S; i &lt;= B; i++) {
        res += dp[T&amp; 0x1][i] % MOD;
    }
    cout &lt;&lt; res % MOD;
    return 0; 
}
```

&nbsp;