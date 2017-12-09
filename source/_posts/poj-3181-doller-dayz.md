---
title: POJ 3181 Doller Dayz 题解
id: 158
date: 2017-03-08 11:39:03
tags:
---


[POJ 3181 Doller Dayz](http://poj.org/problem?id=3181)


## 题目大意

FJ 拿着 N 元钱去商店，发现有价格分别为 1~K 的商品无限多个。问他可以有多少种方法把钱花完。

## 思路

非常基础的完全背包问题，内层循环顺序 + 一维数组可以解决。难点在于这题答案没有取模，非常非常大的整数。因此自己写了 Bign 的类实现加法。

【题目没有取模的话要考虑这一点，不然 WA 到死都不知道为啥……考场上可没有 discuss 看T T】


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <memory.h>

using namespace std;

const int MAX_D = 35;
class Bign {
public:
    int num[MAX_D];
    Bign() {
        memset(num, 0, sizeof(num));
    }
    void operator = (int input[MAX_D]) {
        for (int i = 0; i &lt; MAX_D; i++) {
            num[i] = input[i];
        }
    }
    Bign operator + (Bign &amp; p) {
        Bign res;
        int carry = 0;
        int mod = 0;
        for (int i = MAX_D - 1; i &gt;= 0; i--) {
            int sum = num[i] + p.num[i] + carry;
            mod = sum % 10;
            carry = sum / 10; 
            res.num[i] = mod;
        }
        return res;
    }
};

const int MAX_N = 1000;
const int MAX_K = 100;
int N;
int K;
Bign dp[MAX_N + 4];

void solve() {
    dp[0].num[MAX_D - 1]= 1;
    for (int i = 1; i &lt;= K; i++) {
        for (int j = i; j &lt;= N; j++) {
            dp[j] = dp[j] + dp[j - i];
        }
    }

    int s;
    for (int i = 0; i &lt; MAX_D; i++) {
        if (dp[N].num[i]) {
            s = i;
            break;
        } 
    } 
    for (int i = s; i &lt; MAX_D; i++) {
        cout &lt;&lt; dp[N].num[i];
    }
}

int main() {
    cin &gt;&gt; N &gt;&gt; K;
    solve();
    return 0;
}
```

&nbsp;