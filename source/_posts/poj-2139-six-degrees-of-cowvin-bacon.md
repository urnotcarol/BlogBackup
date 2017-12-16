---
title: POJ 2139 Six Degrees of Cowvin Bacon 题解
id: 273
date: 2017-03-31 11:54:12
tags:
- 算法
- OJ
categories:
- 算法
- 最短路径
---

[POJ 2139 Six Degrees of Cowvin Bacon](http://poj.org/problem?id=2139)

## 题意

“六度牛脉”理论：N 只奶牛组成了 M 个小圈子，每个小圈子内的奶牛到彼此的度为 1，如果两只奶牛 A 和 B 不在同一个圈子，但可以通过 C 认识（C 既和 A 在一个圈子，也和 B 在一个圈子 ），那么她们俩之间的度为 2，求哪只奶牛的牛脉最广，即到其他所有奶牛的度的平均数最小，乘 100 输出。

## 思路

建图的时候把一个圈子内牛两两相连，就符合题意。接下来就是多源最短路径问题。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <vector>
#define ONLINE_JUDGE
using namespace std;

const int MAX_E =  (301 * 300) / 2 + 4;
const int MAX_N = 300 + 4;
const int INF = 0x3f3f3f3f;
typedef pair<int, int> Edge;
vector<pair<int, int> > edge;
int d[MAX_N];
int cow[MAX_N];
int N;
int M;

int BellmanFord(int s) {
    fill(d, d + MAX_N, INF);
    d[s] = 0;

    while (true) {
        bool update = false;
        for (int i = 0; i < edge.size(); i++) {
            Edge e = edge[i];
            if (d[e.first] != INF && d[e.second] > d[e.first] + 1) {
                update = true;
                d[e.second] = d[e.first] + 1;
            }
        }
        if (!update) {
            break;
        }
    }

    int res = 0;
    for (int i = 0; i < N; i++) {
        res += d[i + 1];
    }
    return res;
}

int main() {
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif
    scanf("%d %d", &N, &M);
    int num;
    for (int i = 0; i < M; i++) {
        scanf("%d", &num);
        for (int j = 0; j < num; j++) {
            scanf("%d", &cow[j]);
        }
        for (int j = 0; j < num - 1; j++) {
            for (int k = j + 1; k < num; k++) {
                edge.push_back(Edge(cow[j], cow[k]));
                edge.push_back(Edge(cow[k], cow[j]));
            }
        }
    }
    int minD = INF;
    for (int i = 0; i < N; i++) {
        minD = min(minD, BellmanFord(i + 1));
    }
    printf("%d", 100 * minD / (N - 1));
    return 0;
}
```

<table id="listStatus" class="table table-striped table-bordered table-responsive dataTable no-footer hover-date" width="100%" cellspacing="0">
<tbody>
<tr id="8612476" class="accepted odd">
<td class=" run-id hidden-lg-down"><span style="font-size: 10pt;">8612476</span></td>
<td class=" username">
<div><span style="font-size: 10pt;">[carolunar](https://vjudge.net/user/carolunar)</span></td>
<td class=" oj"><span style="font-size: 10pt;">POJ</span></td>
<td class=" prob_num">
<div><span style="font-size: 10pt;">[2139](https://vjudge.net/problem/POJ-2139)</span></td>
<td class=" status hidden-md-down">
<div class="view-solution" title="" data-toggle="tooltip" data-run-id="8612476" data-original-title=""><span style="font-size: 10pt;">Accepted</span></td>
<td class=" runtime"><span style="font-size: 10pt;">16</span></td>
<td class=" memory"><span style="font-size: 10pt;">0.2</span></td>
<td class=" length hidden-lg-down"><span style="font-size: 10pt;">1511</span></td>
<td class=" language">
<div class="view-solution shared" title="" data-toggle="tooltip" data-html="true" data-run-id="8612476" data-original-title="C++"><span style="font-size: 10pt;">C++</span></td>
<td class=" date">
<div class="localizedTime" data-time="1490931171000"><span class="absolute" style="font-size: 10pt;">2017-03-31 11:32:51</span></td>
</tr>
</tbody>
</table>