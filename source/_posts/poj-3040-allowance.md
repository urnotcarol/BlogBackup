---
title: POJ 3040 Allowance 题解
id: 119
date: 2017-02-12 22:18:48
tags:
- 算法
- OJ
categories:
- 算法
- 贪心
---

[POJ 3040 Allowance](http://poj.org/problem?id=3040)

题意：

农夫要给奶牛Bessie每周津贴。农夫有N种不同面额不同数量的硬币，而且相邻大小的硬币面额存在整除关系（1分、5分、10分、50分）。他每周至少要给奶牛C分钱，计算他所有的钱最多可以给奶牛多少周。

思路：

有限的钱要维持尽量多的时间，关键在于每周尽可能少的超过最低标准。因此，对所有的硬币分两类，面额大于C的硬币一天给一个。面额小于C的硬币……没有想到合适的贪心策略了。

参考了：[码农场](http://www.hankcs.com/) » [POJ 3040 Allowance 题解 《挑战程序设计竞赛(第2版)》](http://www.hankcs.com/program/cpp/poj-3040-allowance.html)

大硬币面额是小硬币面额的倍数很重要，这意味着同等数量的一堆小硬币可以被一枚大硬币代替，这样小硬币就可以剩下来以后用，可以保证最小的浪费，所以

1\. 从大到小贪心，尽量多的选择大硬币，但是不要超过所需金额c

2\. 从小到大贪心。

1、2都执行完之后，如果所需的金额还是大于0，就说明硬币不够再多给一周了，break。
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <math.h>

using namespace std;

int n;
int m = 0; //需要进行贪心的硬币 
int c;
pair<int, int> coin[20];

int solve() {
    int res = 0;
    sort(coin, coin + m);
    int need = c;
    while (1) {
        for (int i = m - 1; i >= 0; --i) {
            if (need > 0 && coin[i].second > 0) {
                int t = min(coin[i].second, need / coin[i].first);
                need -= t * coin[i].first;
                coin[i].second -= t;  
            }   
        } 
        for (int i = 0; i < m; i++) {
            if (need > 0 && coin[i].second > 0) {
                int t = min(coin[i].second, (int)ceil((double)need / (double)(coin[i].first)));
                need -= t * coin[i].first;
                coin[i].second -= t;     
            }

        }
        if (need > 0) {
            break;
        }
        res++;
        need = c;  
    }
    return res;
}

int main() {
    int res = 0;
    int v;
    int b;
    scanf("%d %d", &n, &c);
    for (int i = 0; i < n; ++i) {
        scanf("%d %d", &v, &b);
        if (v >= c) {
            res += b;
        }
        else {
            coin[m].first = v;
            coin[m].second = b;
            m++;
        }
    }
    res += solve();

    printf("%d\n", res); 
    return 0;
}
```

&nbsp;