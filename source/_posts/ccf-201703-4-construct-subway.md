---
title: CCF 201703-4 地铁修建 题解 并查集
id: 320
date: 2017-04-05 10:43:44
tags:
- 算法
- OJ
categories:
- 算法
- 并查集
---

### 题目
A市有n个交通枢纽，其中1号和n号非常重要，为了加强运输能力，A市决定在1号到n号枢纽间修建一条地铁。

地铁由很多段隧道组成，每段隧道连接两个交通枢纽。经过勘探，有m段隧道作为候选，两个交通枢纽之间最多只有一条候选的隧道，没有隧道两端连接着同一个交通枢纽。

现在有n家隧道施工的公司，每段候选的隧道只能由一个公司施工，每家公司施工需要的天数一致。而每家公司最多只能修建一条候选隧道。所有公司同时开始施工。

作为项目负责人，你获得了候选隧道的信息，现在你可以按自己的想法选择一部分隧道进行施工，请问修建整条地铁最少需要多少天。

<!-- more -->

### 输入格式
输入的第一行包含两个整数n, m，用一个空格分隔，分别表示交通枢纽的数量和候选隧道的数量。

第2行到第m+1行，每行包含三个整数a, b, c，表示枢纽a和枢纽b之间可以修建一条隧道，需要的时间为c天。
### 输出格式
输出一个整数，修建整条地铁线路最少需要的天数。

### 样例输入
```
6 6
1 2 4
2 3 4
3 6 7
1 4 2
4 5 5
5 6 6
```
### 样例输出
`6`
### 思路
将所有边加入最小值优先队列，每次出队的即为剩下的边中最小的。每条边出队时，将这条边的两个顶点加入并查集的同一小组，判断 1 和 n 号枢纽是否连通。一旦连通，当前出队的这条边的权值即为所求答案。

PS，这道题考试的时候毫无思路。之后郑同学提出了一个思路，是这样的：求出当前所有非零边的权值中的最小值 minCost，然后给所有边的权值都减去这一 minCost，这时判断是否存在一条从起点到终点路径和为 0 的最短路。一旦存在，那么之前减去的所有 minCost 值之和就是答案。提交后发现答案错误只得了 5 分，应该是复杂度太高。然后发现，其实和上面 AC 的答案的思路的本质是一样的，都是不断找出所有的最短边，看是否能组成一条从起点到终点的路径。然鹅这两种思路都不是我想出来的Orz... 还需要一些想象力和变通能力（菜.jpg）


<!-- more -->
### 代码
```
#include <iostream>
#include <stdio.h>
#include <queue>
#include <algorithm>

using namespace std;

const int MAX_N = 100000 + 16;
const int MAX_M = 200000 + 16;

int n;
int m;
struct Edge {
    int u, v, cost;
    Edge() {}
    Edge(int u, int v, int cost): u(u), v(v), cost(cost) {}
    bool operator> (const Edge & e) const {
        return cost > e.cost;
    }
};
Edge edge[MAX_M];

int par[MAX_N];
int rank[MAX_N];

void init(int n) {
    for (int i = 0; i < n; i++) {
        par[i] = i;
        rank[i] = 0;
    }
}

int find(int x) {
    if (par[x] == x) {
        return x;
    }
    return find(par[x]);
}

void unite(int x, int y) {
    x = find(x);
    y = find(y);
    if (x == y) {
        return;
    }
    if (rank[x] < rank[y]) {
        par[x] = y;
    }
    else {
        par[y] = x;
        if (rank[x] == rank[y]) {
            rank[x]++;
        }
    }
}

bool same(int x, int y) {
    return find(x) == find(y);
}

int main() {
    scanf("%d %d", &n, &m);
    init(n + 1);
    int u, v, cost;
    priority_queue<Edge, vector<Edge>, greater<Edge> > que;
    for (int i = 0; i < m; i++) {
        scanf("%d %d %d", &u, &v, &cost);
        que.push(Edge(u, v, cost));
    }

    int res = 0;
    while (!que.empty()) {
        Edge e = que.top();
        que.pop();
        unite(e.u, e.v);
        if (same(1, n)) {
            res = e.cost;
            break;
        }
    }
    cout << res;
    return 0;
```