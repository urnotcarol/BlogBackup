---
title: POJ 1742 Coins 题解
id: 155
date: 2017-03-08 10:53:08
tags:
---


[POJ 1742 Coins](http://poj.org/problem?id=1742)


## 题目大意

n 种硬币，面值和数量各异。能拼成 1~m 中的多少个值。

## 思路

书上例题多重部分和问题的稍微复杂版。

用 DP 求取 bool 结果的话会有不少浪费。

因此这里用 dp[i + 1][j] := 用前 i 种数加和得到 j 时第 i 种数最多能剩余多少个（不能加和得到 j 的情况下为 -1）。

只要看最终是否满足 dp[n][m] 是否大于等于 0 ，就得到答案。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <memory.h>

using namespace std;

const int MAX_N = 100;
const int MAX_M = 100000;
int n;
int m;
int a[MAX_N + 3];
int c[MAX_N + 3];
int dp[MAX_M + 3];

void solve() {
    memset(dp, -1, sizeof(dp));   
    dp[0] = 0; 
    for (int i = 1; i &lt;= n; i++) {
        for (int j = 0; j &lt;= m; j++) {
            if (dp[j] &gt;= 0) {    //前 i - 1 个数已经能够加和组成 j, 所以第 i 个数全部剩下
                dp[j] = c[i];
            }
            else if (j &lt; a[i] || dp[j - a[i]] &lt;= 0) { //加上这个数也不能组成 j
                dp[j] = -1;
            }
            else {   
                dp[j] = dp[j - a[i]] - 1;
            }
        }
    }
    int count = 0;
    for (int i = 1; i &lt;= m; i++) {
        if(dp[i] &gt;= 0) {
            count++;
        }
    }
    cout &lt;&lt; count &lt;&lt; endl;
}

int main() {
    while(scanf("%d %d", &amp;n, &amp;m) &amp;&amp; n) {
        for (int i = 1; i &lt;= n; i++) {
            scanf("%d", &amp;a[i]);
        }
        for (int i = 1; i &lt;= n; i++) {
            scanf("%d", &amp;c[i]);
        }
        solve();
    }
    return 0;    
}
```

&nbsp;