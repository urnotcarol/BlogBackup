---
title: POJ 3259 Wormholes 题解 Bellman-Ford 负圈
id: 296
date: 2017-04-04 11:22:45
tags:
---

[POJ 3259 Wormholes](http://poj.org/problem?id=3259)

## 题意

FJ 有 N 块田地，由 M 条双向道路相连，神奇的是还有 W 个单向的虫洞相连。虫洞是什么呢，就是你经过这个洞之后就会回到过去。求 FJ 是否有可能在某个圈中绕一圈后回到过去。

## 思路

图中有正权无向边和负权有向边，其实就是求是否存在负圈。建好图之后，用 BellmanFord 算法可以检查出负圈的存在。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <vector>
#include <memory.h>

#define ONLINE_JUDGE
using namespace std;

const int MAX_N = 500 + 4;
const int INF = 0x3f3f3f3f;

struct Edge {
    int from;
    int to;
    int cost;
    Edge() {}
    Edge(int from, int to, int cost) {
        this-&gt;from = from;
        this-&gt;to = to;
        this-&gt;cost = cost;
    }
};
vector<Edge> edge;

int cost[MAX_N][MAX_N];

int d[MAX_N];
int F;
int N;
int M;
int W;

bool BellmanFord () {
    memset(d, 0, sizeof(d));
    for (int v = 0; v &lt; N; v++) {
        for (int i = 0; i &lt; edge.size(); i++) {
            Edge e = edge[i];
            if (d[e.to] &gt; d[e.from] + e.cost) {
                d[e.to] = d[e.from] + e.cost;
                if (v == N - 1) {
                    return true;
                }
            }
        }
    }
    return false;
}

int main() {
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif
    scanf("%d", &amp;F);
    while (F--) {
        edge.clear();
        scanf("%d %d %d", &amp;N, &amp;M, &amp;W);
        int from, to, cost;
        for (int i = 0; i &lt; M; i++) {
            scanf("%d %d %d", &amp;from, &amp;to, &amp;cost); 
            Edge e1(from, to, cost);
            Edge e2(to, from, cost);
            edge.push_back(e1);
            edge.push_back(e2);
        }
        for (int i = 0; i &lt; W; i++) {
            scanf("%d %d %d", &amp;from, &amp;to, &amp;cost);
            Edge e(from, to, -cost);
            edge.push_back(e);
        }
        bool res = BellmanFord();
        if (res) {
            printf("YES\n");
        }
        else {
            printf("NO\n");
        }
    }
    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    return 0;
}
```

