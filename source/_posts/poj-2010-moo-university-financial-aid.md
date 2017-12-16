---
title: POJ 2010 Moo University - Financial Aid 题解
id: 223
date: 2017-03-15 10:39:19
tags:
- 算法
- OJ
categories:
- 算法
- 优先队列
---

[POJ 2010 Moo University - Financial Aid](http://poj.org/problem?id=2010)

## 题目大意

Bessie 办了一个奶牛大学，要从 C 只奶牛中招 N 只上学。每只奶牛的高考分数和需要的助学金不同，Bessie 希望所有学生分数的中位数尽可能高，同时只有 F 元的助学金可以发。求最大的中位数是多少。

## 思路

先对奶牛按分数排序，只有分数处于中间的一部分奶牛才有可能是中位数。遍历这些奶牛，算出每一只作为中位数时，比它分数低的奶牛所需要的最少助学金 lower[i], 比它分数高的奶牛所需要的最少助学金 upper[i]。最后根据分数从大到小遍历这些奶牛，满足 lower[i] + 助学金[i] + upper[i] <= F 的第一只奶牛，即为答案。

这道题对优先队列的使用在于求 lower[i] 和 upper [i] 的时候，这也是我没有想的很好的地方。起初我对每只奶牛都建立了一个最小优先队列，把其前面的所有牛的助学金都放进去，再出队 C/2 个，自以为很厉害，可以说是熟练运用优先队列了呢，这次收到了来自 TLE 的无声嘲讽，还是图样。

更优的做法是只建立一个优先队列，因为每次也只有一个值可能被更新。看代码吧~

## AC 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <queue>
#include <functional>

using namespace std;

const int MAX_C = 100000;
int N;
int C;
int F;
pair<int, int> cow[MAX_C + 4];
int lower[MAX_C + 4];
int upper[MAX_C + 4];

int main() {
    scanf("%d %d %d", &N, &C, &F);
    for (int i = 0; i < C; i++) {
        scanf("%d %d", &cow[i].first, &cow[i].second);
    }

    sort(cow, cow + C);
    int first = N / 2;
    int last = C - first - 1;

    priority_queue<int> que;
    int total = 0;
    for (int i = 0; i < first; i++) {
        total += cow[i].second;
        que.push(cow[i].second);        
    }

    for (int i = first; i <= last; i++) {
        lower[i] = total;
        int aid = cow[i].second;
        int top = que.top();
        if (aid < top) {
            que.pop();
            que.push(aid);
            total = total - top + aid;
        }        
    }

    while (que.size()) {
        que.pop();
    }

    total = 0;
    for (int i = last + 1; i < C; i++) {
        total += cow[i].second;
        que.push(cow[i].second);
    }

    for (int i = last; i >= first; i--) {
        upper[i] = total;
        int aid = cow[i].second;
        int top = que.top();
        if (aid < top) {
            que.pop();
            que.push(aid);
            total = total - top + aid;
        }
    }

    int res = -1;
    for (int i = last; i >= first; i--) {
        if (lower[i] + cow[i].second + upper[i] <= F) {
            res = cow[i].first;
            break;
        }
    }
    cout << res;
    return 0;
}
```

