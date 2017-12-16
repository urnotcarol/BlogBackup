---
title: POJ 3280 Cheapest Palindrome 题解
id: 159
date: 2017-03-09 11:32:36
tags:
- 算法
- OJ
categories:
- 算法
- 字符串
---


[POJ 3280 Cheapest Palindrome](http://poj.org/problem?id=3280)


## 题目大意

给定一个长度为 M 的字符串，由小写字母表中的前 N 个字母组成（这点很 tricky），每个字母的添加删除都有相应的代价。求通过在任意位置增删，把该字符串变为回文字符串的最小代价。

## 思路

这道题虽然 discuss 里面都说是大水题……但由于没有接触过这种新的递推式，所以理解别人的解题报告对我来说也还是有点挑战性。

令 dp[i][j] 表示将子串 i...j 变为回文的最小代价，方程为：

dp[i][j] = min(dp[i][j - 1] + cost[j位置的字符], dp[i + 1][j] + cost[i位置的字符]);

要注意的是，添加删除的本质是一样的，在某个位置添加一个字符等效于在轴对称的位置删除这个字符，因此 cost 直接取二者里面较小值。

此外 i, j 的方向也需要特别注意，i 是从末尾到开头，j 是从 i + 1 到末尾。这样可以保证每一步都用到了前一步的结果，并且循环结束后得到了将整个字符串变为回文的最小代价。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>

using namespace std;

const int MAX_M = 2000;
int N;
int M;
char tag[MAX_M + 3];
int dp[MAX_M + 3][MAX_M + 3];
int cost[26 + 3];

int main() {
    scanf("%d %d", &N, &M);
    cin.ignore();
    for (int i = 0; i < M; i++) {
        scanf("%c", &tag[i]);
    }
    cin.ignore();

    char tempChar;
    int add;
    int del;
    for (int i = 0; i < N; i++) {
        scanf("%c %d %d", &tempChar, &add, &del);
        cost[tempChar - 'a'] = min(add, del);
        cin.ignore();
    }

    for (int i = M - 1; i >= 0; i--) {
        for (int j = i + 1; j < M; j++) {
            if (tag[i] == tag[j]) {
                dp[i][j] = dp[i + 1][j - 1];
            }
            else {
                dp[i][j] = min(dp[i][j - 1] + cost[tag[j] - 'a'], dp[i + 1][j] + cost[tag[i] - 'a']);
            }
        }
    }

    cout << dp[0][M - 1];

    return 0; 

}
```