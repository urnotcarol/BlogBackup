---
title: POJ 1159 Palindrome 题解
id: 282
date: 2017-04-03 11:02:40
tags:
---

[POJ 1159 Palindrome](https://vjudge.net/problem/POJ-1159)

## 题意

给定一个长度为 N 的字符串 S，求最少添加几个字符可以让该串变成回文串。

## 思路

令 S' 为 S 的逆序串，求出 S 和 S' 的最长公共子串的长度 L, 则 N - L 即为结果。

## 注意

直接用 DP 的话，需要 N * N 的数组，会 MLE, 因此压缩成 2 * N 的数组。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <memory.h>
#include <algorithm>

using namespace std;

const int MAX_N = 5000;
int N;
char str[MAX_N + 4];
char rts[MAX_N + 4];

int dp[2][MAX_N + 4];

int main() {
    cin &gt;&gt; N;
    cin.ignore();
    for (int i = 1; i &lt;= N; i++) {
        scanf("%c", &amp;str[i]);
        rts[N + 1 - i] = str[i];
    } 
    for (int i = 1; i &lt;= N; i++) {
        int cur = i &amp; 0x1;
        int pre = (i - 1) &amp; 0x1;
        memset(dp[cur], 0, sizeof(dp[cur]));
        for (int j = 1; j &lt;= N; j++) {
            if (str[i] == rts[j]) {
                dp[cur][j] = dp[pre][j - 1] + 1;
            }
            else {
                dp[cur][j] = max(dp[pre][j], dp[cur][j - 1]);
            }
        }
    }
    cout &lt;&lt; N - dp[N &amp; 0x1][N];
    return 0;
}
```


&nbsp;