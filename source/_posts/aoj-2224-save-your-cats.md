---
title: AOJ 2224 Save your cats 题解 Kruskal 最小生成树
id: 314
date: 2017-04-04 19:57:28
tags:
- 算法
- OJ
categories:
- 算法
- 最小生成树
---

[AOJ 2224 Save your cats](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2224)

## 题意

救猫猫！花园里有很多可爱的小猫，邪恶的巫婆嫉妒这些小猫，就施魔法用篱笆把它们围起来了。主人要去救这些小猫，但是篱笆只有圣水才能破开，长度为 L 的篱笆需要数量为 L 的圣水。然而圣水又非常贵，给定每条篱笆的头尾的位置（都是坐标系中的格点），求最少花费多少圣水才能救出所有小猫。

## 思路

建图略微有一点点的麻烦，给出每条边顶点的坐标，需要自己计算出权值。建好图之后，问题就转化成在去掉的边的权值最小的情况下把图变成树，所以就是一个求最大权生成树的问题。但是结果不是最大权和，而是去掉的边的和。依然转化成求最小生成树。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <math.h>
#include <vector>

#define ONLINE_JUDGE

using namespace std;

const int MAX_N = 10000 + 4;
int N;
int M;
struct P {
    int x, y;
    P() {}
    P(int x, int y): x(x), y(y) {}
};

struct Edge {
    int u, v;
    double cost;
    Edge() {}
    Edge(int u, int v, double cost): u(u), v(v), cost(cost) {}
};

bool comp(Edge & e1, Edge & e2) {
    return e1.cost < e2.cost;
}

P pile[MAX_N];
vector<Edge> edge;
double sum;
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
    return par[x] = find(par[x]);
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

void kruskal() {
    init(N);
    sort(edge.begin(), edge.end(), comp);
    for (int i = 0; i < edge.size(); i++) {
        Edge e = edge[i];
        if (!same(e.u, e.v)) {
            unite(e.u, e.v);
            sum -= e.cost;
        }        
    }
}

int main() {
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif
    scanf("%d %d", &N, &M);
    int x, y;
    for (int i = 1; i <= N; i++) {
        scanf("%d %d", &pile[i].x, &pile[i].y);
    }
    int u, v;
    double cost;
    sum = 0;
    for (int i = 0; i < M; i++) {
        scanf("%d %d", &u, &v);
        cost = -sqrt(pow((pile[u].x - pile[v].x), 2) + pow((pile[u].y - pile[v].y), 2));
        sum += cost;
        edge.push_back(Edge(u, v, cost));
    }
    kruskal();

    if (sum == 0.0) {
        printf("0.000");
    }
    else {
        printf("%.3lf", -sum);
    }
    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    return 0;
}
```

