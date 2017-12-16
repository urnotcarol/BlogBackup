---
title: AOJ 2249 Road Construction 题解 Dijkstra 最短路
id: 302
date: 2017-04-04 16:50:16
tags:
- 算法
- OJ
categories:
- 算法
- 最短路径
---

[AOJ 2249 Road Construction](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2249)

## 题意

ACM 王国要修路。现有一个修路方案，此方案给出了连接王国的首都和其他城市的一些路线的距离和费用，可以确保任意两个城市之间是连通的。现在为了降低预算，需要修改这个方案。要求修改后的方案依然保持任意两点的连通性并且，首都到其他每个城市的最短距离不要变，在这两个前提下，求出修改后的方案的最低预算。

## 思路

这是一个单源最短路问题。可以看成在给定的一个地图上，要求出首都到其他各个城市的最短路的建设费用之和。

求最短路倒是简单，但是这个最少费用怎么求有点麻烦。看了别人的代码，思路是，在求出单源最短路之后，遍历除首都外的每个点 i，看下这条最短路是经过 i 的哪一个邻居结点来到 i 的，假设为 t，那么费用就在原来的基础上加上 i 与 t 之间的建设费用。这样在遍历所有结点的同时，也遍历了所有结点的最短路，同时不会出现同一条边的费用被计算两次的情况。假设边 e(i, j) 是首都到结点 j 的最短路上的最后一段，那么在遍历到 j 的时候就会加上这一条边的费用。而遍历到 i 的时候，首都到 i 的最短路必然不会经过边 e(i, j)，也就不会被重复计算。在这个过程中，每个结点只关心与自己相连的那条路的费用，等全部遍历完成的时候，自然就得出了整体的最少费用。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <queue>
#include <vector>
#include <memory.h>
#define ONLINE_JUDGE
using namespace std;

const int INF = 0x3f3f3f3f;
const int MAX_N = 10000 + 4;
struct Edge {
    int to;
    int cost;
    int distance;
    Edge() {}
    Edge(int to, int cost, int distance): to(to), cost(cost), distance(distance) {}
};
typedef pair<int, int> P;
vector<Edge> G[MAX_N];
int d[MAX_N];
int N;
int M;

void dijkrast(int s) {
    priority_queue<P, vector<P>, greater<P> > que;
    fill(d, d + MAX_N, INF);
    d[s] = 0;
    que.push(P(0, s));
    while (!que.empty()) {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (d[v] < p.first) {
            continue;
        }
        for (int i = 0; i < G[v].size(); i++) {
            Edge e = G[v][i];
            if (d[e.to] > d[v] + e.distance) {
                d[e.to] = d[v] + e.distance;
                que.push(P(d[e.to], e.to));
            }
        }
    }     
}

int main() {
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif
    while (scanf("%d %d", &N, &M) && N) {
        for (int i = 0; i < MAX_N; i++) {
            G[i].clear();
        }
        int u, v, distance, cost;
        for (int i = 0; i < M; i++) {
            scanf("%d %d %d %d", &u, &v, &distance, &cost);
            G[u].push_back(Edge(v, cost, distance));
            G[v].push_back(Edge(u, cost, distance));
        }
        dijkrast(1);
        int res = 0;
        for (int i = 2; i <= N; i++) {
            int minCost = INF;
            for (int j = 0; j < G[i].size(); j++) {
                if (d[i] == d[G[i][j].to] + G[i][j].distance) {
                    minCost = min(minCost, G[i][j].cost);
                }
            }
            res += minCost;
        }
        printf("%d\n", res);
    }
    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    return 0;
}

```

&nbsp;
<table id="listStatus" class="table table-striped table-bordered table-responsive dataTable no-footer hover-date" width="100%" cellspacing="0">
<tbody>
<tr id="8550024" class="accepted odd">
<td class=" run-id hidden-lg-down"><span style="font-size: 10pt;">8550024</span></td>
<td class=" username">
<div><span style="font-size: 10pt;">[carolunar](https://vjudge.net/user/carolunar)</span></td>
<td class=" oj"><span style="font-size: 10pt;">Aizu</span></td>
<td class=" prob_num">
<div><span style="font-size: 10pt;">[2249](https://vjudge.net/problem/Aizu-2249)</span></td>
<td class=" status hidden-md-down">
<div class="view-solution" title="" data-toggle="tooltip" data-run-id="8550024" data-original-title=""><span style="font-size: 10pt;">Accepted</span></td>
<td class=" runtime"><span style="font-size: 10pt;">50</span></td>
<td class=" memory"><span style="font-size: 10pt;">5.1</span></td>
<td class=" length hidden-lg-down"><span style="font-size: 10pt;">1916</span></td>
<td class=" language">
<div class="view-solution shared" title="" data-toggle="tooltip" data-html="true" data-run-id="8550024" data-original-title="C++"><span style="font-size: 10pt;">C++</span></td>
<td class=" date">
<div class="localizedTime" data-time="1490414063000"><span class="absolute" style="font-size: 10pt;">2017-03-25 11:54:23</span></td>
</tr>
</tbody>
</table>

* * *

## 参考

[码农场](http://www.hankcs.com/) » [AOJ 2249 Road Construction 题解 《挑战程序设计竞赛》](http://www.hankcs.com/program/cpp/aoj-2249-road-construction.html)

&nbsp;