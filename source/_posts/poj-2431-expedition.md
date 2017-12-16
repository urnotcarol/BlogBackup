---
title: POJ 2431 Expedition 题解
id: 165
date: 2017-03-10 16:15:55
tags:
- 算法
- OJ
categories:
- 算法
- 优先队列
---

[
POJ 2431 Expedition
](http://poj.org/problem?id=2431)

## 题意

车子坏了，奶牛们要去修理厂修车。油箱里的汽油每公斤只能走一公里，初始距离修理厂 L 公里，有 P 公斤汽油。沿途有 N 个加油站，距离修理厂的距离以及可以加的汽油量各异。车子虽破，但却有一个巨大的油箱，想加多少就加多少。求车能不能开到修理厂，能的话求出最少加油次数，否则输出 -1 。

## 思路

这是书上 2.4 加工并存储的数据结构 这一节的例题。用优先队列。

优先队列是一种特殊的队列。普通的队列中，元素进入就排到队尾，出的话队头先出。优先队列是有优先权的元素先出，同样，一个元素进入的时候，就按其优先权排到自己该有的位置，就像做人一样，要搞清楚自己的定位。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <queue>
#include <algorithm>

using namespace std;

const int MAX_N = 10000;
int N;
pair<int, int> stop[MAX_N + 4];
int L;
int P;

void solve() {
    priority_queue<int> que;

    stop[N].first = L;
    stop[N].second = 0;
    N++;

    int ans = 0;
    int pos = 0;
    int tank = P;

    for (int i = 0; i < N; i++) {
        int d = stop[i].first - pos;  //distance to next fuel stop
        while (tank < d) {
            if (que.empty()) {
                printf("-1");
                return;
            }
            tank += que.top();
            que.pop();
            ans++;
        }

        tank -= d;
        pos = stop[i].first;
        que.push(stop[i].second);
    }

    printf("%d", ans);
}

int main() {
    scanf("%d", &N);

    for (int i = 0; i < N; i++) {
        scanf("%d %d", &stop[i].first, &stop[i].second);
    }
    scanf("%d %d", &L, &P);

    for (int i = 0; i < N; i++) {
        stop[i].first = L - stop[i].first;  //将距终点的距离改为距起点的距离，方便之后操作
    }
    sort(stop, stop + N);
    solve();
    return 0;
}
```

