---
title: POJ 2377 Bad Cowtractors 题解 Prim 最小生成树
id: 309
date: 2017-04-04 19:41:13
tags:
- 算法
- OJ
categories:
- 算法
- 最短路径
---

## 题意

FJ 雇 Bessie 帮他在不同的牛棚之间修建互联网，给定 M 条 连接 1 - N 号牛棚的线路及其费用，要求最终在两两互通的情况下花费最小。但他不打算给 Bessie 发工资，于是 Bessie 准备不好好干活，给他用最贵最贵的建设方法。

## 思路

将所有道路的权重变成相反数，按照求最小生成树的算法，结果再取相反数就是最大生成树。

Prim 算法和求最短路的 Dijkrast 算法很相似。区别在于后者是从尚未使用过的顶点中选择一个距离源点最近的点，而 Prim 算法是从尚未使用过的点中选择 X（已使用的点集） 到其最近的点。

## 注意

有可能本来就不连通，这时输出 -1。因此需要对输入用并查集检查连通性。每输入一条边就将其两个顶点放入并查集的一个小组内，最后一旦小组数超过 1，就说明是非连通图。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <vector>
#include <queue>
#define ONLINE_JUDGE

using namespace std;

const int MAX_N = 1000 + 4;
const int INF = 0x3f3f3f3f;
struct Edge {
    int to;
    int cost;
    Edge() {}
    Edge(int to, int cost): to(to), cost(cost) {}
};
vector<Edge> G[MAX_N];

int minCost[MAX_N];
bool used[MAX_N];
typedef pair<int, int> P;
int N;
int M;

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

int prim() {
    fill(minCost, minCost + N, INF);
    fill(used, used + N, false);
    minCost[0] = 0;
    priority_queue<P, vector<P>, greater<P> > que;
    que.push(P(0, 0));

    int res = 0;
    while (!que.empty()) {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (minCost[v] < p.first) {
            continue;
        }
        used[v] = true;
        res += p.first;
        for (int i = 0; i < G[v].size(); i++) {
            Edge e = G[v][i];
            if (!used[e.to] && minCost[e.to] > e.cost) {
                minCost[e.to] = e.cost;
                que.push(P(minCost[e.to], e.to));
            }
        }
    }
    return res;
}

int main() {
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif
    scanf("%d %d", &N, &M);
    init(N);
    int from, to, cost;
    for (int i = 0; i < M; i++) {
        scanf("%d %d %d", &from, &to, &cost);
        from--;
        to--;
        unite(from, to);
        G[from].push_back(Edge(to, -cost));
        G[to].push_back(Edge(from, -cost));
    }
    int res = -1;
    bool connected = true;
    for (int i = 1; i < N; i++) {
        if (!same(0, i)) {
            connected = false;
            break;
        }
    }
    if (connected) {
        res = -prim();
    }
    cout << res;
    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    return 0;
}
```

&nbsp;

&nbsp;