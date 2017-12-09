---
title: POJ 2395 Out of Hay 题解 Kruskal 最小生成树
id: 317
date: 2017-04-04 20:05:52
tags:
---

[POJ 2395 Out of Hay](http://poj.org/problem?id=2395)

## 题意

奶牛们的草要吃光了，这是非常可怕的事情。Bessie 决定走访每一家农场，看看她们的粮草情况。旅途艰难，Bessie 每走一公里就要喝一升水。给出连接 N 个农场的 M 条双向道路的长度，Bessie 想知道，自己走访完所有农场至少需要带一个多大的水壶。因为每到一个农场都可以灌满水壶，因此水壶容量只要够走最长的一条路就行。

## 思路

求最小生成树的所有边中，权值最大的一条。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <vector>
#include <algorithm>
#define ONLINE_JUDGE

using namespace std;

const int MAX_N = 2000 + 4;
const int MAX_M = 10000 + 4;

int N;
int M;

struct Edge {
    int u, v, cost;
};
bool comp(Edge &amp; e1, Edge &amp; e2) {
    return e1.cost &lt; e2.cost;
}

Edge edge[MAX_M];

int par[MAX_N];
int rank[MAX_N];

void init(int n) {
    for (int i = 0; i &lt; n; i++) {
        par[i] = i;
        rank[i] = 0;
    }
}

int find(int x) {
    if (par[x] == x) {
        return x;
    }
    return par[x] = find(par[x]);
}

void unite(int x, int y) {
    x = find(x);
    y = find(y);
    if (x == y) {
        return;
    }
    if (rank[x] &gt; rank[y]) {
        par[y] = x;
    } 
    else {
        par[x] = y;
        if (rank[x] == rank[y]) {
            rank[y]++;
        }
    }
}

bool same(int x, int y) {
    return find(x) == find(y);
}

int kruskal() {
    init(N);
    int res = 0;
    sort(edge, edge + M, comp);
    for (int i = 0; i &lt; M; i++) {
        Edge e = edge[i];
        if (!same(e.u, e.v)) {
            unite(e.u, e.v);
            res = max(res, e.cost);
        }
    }
    return res;
}

int main() { 
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif

    scanf("%d %d", &amp;N, &amp;M);
    int u, v;
    for (int i = 0; i &lt; M; i++) {        
        scanf("%d %d %d", &amp;u, &amp;v, &amp;edge[i].cost);
        u--;
        v--;
        edge[i].u = u;
        edge[i].v = v;
    }
    int res = kruskal();
    printf("%d", res);

    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    return 0;
}
```


&nbsp;