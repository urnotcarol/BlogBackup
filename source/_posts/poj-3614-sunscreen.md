---
title: POJ 3614 Sunscreen 题解
id: 221
date: 2017-03-14 16:17:36
tags:
- 算法
- OJ
categories:
- 算法
- 优先队列
---

[POJ 3614 Sunscreen](http://poj.org/problem?id=3614)

## 题目大意

奶牛们晒日光浴要擦防晒霜。防晒霜有不同的 SPF 度数，每个奶牛的肤质不同所以要用不同度数的防晒霜，度数太高相当于白晒了，太低的话会晒伤。给定奶牛总数、每只奶牛可以使用的防晒霜度数区间 [minSPF<sub>i , </sub>maxSPF<sub>i</sub>]、每种防晒霜的度数和瓶数，求最多可以让多少只奶牛美美地晒上日光浴。

## 思路

已知这道题用优先队列可以得到较好的解，因为是优先队列的课后习题……

之前一直在做 DP，思维都僵化了Θ_Θ。刚开始遍历了奶牛，把防晒霜装进了优先队列里。自以为很厉害了，然而收到了来自 WA 的无声嘲讽。

核心思想就是“在可以用第 i 瓶防晒霜的所有奶牛中，给最挑剔的奶牛那只先用”。因此每次都放所有可能用这瓶防晒霜的奶牛进入队列，然后让右边界最小的先出队。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <queue>
#include <functional>
#include <algorithm>

using namespace std;

const int MAX_C = 2500;
const int MAX_L = 2500;
int C;
int L;
pair<int, int> cow[MAX_C + 4];
pair<int, int> lotion[MAX_L + 4];

int main() {
    scanf("%d %d", &C, &L);
    for (int i = 0; i < C; i++) {
        scanf("%d %d", &cow[i].first, &cow[i].second);
    }

    priority_queue<int, vector<int>, greater<int> > que;
    for (int i = 0; i < L; i++) {
        scanf("%d %d", &lotion[i].first, &lotion[i].second);
    }
    sort(cow, cow + C);
    sort(lotion, lotion + L);

    int res = 0;
    int cur = 0;
    for (int i = 0; i < L; i++) {
        while (cur < C && cow[cur].first <= lotion[i].first) {
            que.push(cow[cur].second);
            cur++;
        }
        while (!que.empty() && lotion[i].second) {
            int maxSPF = que.top();
            que.pop();
            if (maxSPF >= lotion[i].first) {
                res++;
                lotion[i].second--;
            }
        }
    }
    cout << res;
    return 0;
}
```

&nbsp;