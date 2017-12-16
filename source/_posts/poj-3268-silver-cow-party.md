---
title: POJ 3268 Silver Cow Party 题解 dijkstra 最短路
id: 300
date: 2017-04-04 11:31:29
tags:
- 算法
- OJ
categories:
- 算法
- 最短路径
---

[POJ 3268 Silver Cow Party](http://poj.org/problem?id=3268)

## 题意

N 个农场之间有 M 条单向道路相连，现在每个农场的奶牛都要去 X 号农场参加 party，如果所有奶牛都机智地选择最短路走的话，那么路程最长的奶牛走过的路有多长。

## 思路

有向图的多源最短路径问题。一开始一直 TLE，参考了别人的题解。存疑。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <queue>
#include <vector>

#define ONLINE_JUDGE
using namespace std;

const int MAX_N = 1000 + 4;
const int INF = 0x3f3f3f3f;
int N, M, X;
int d[MAX_N][MAX_N]; 

struct Edge {
    int to;
    int cost;
    Edge () {}
    Edge (int to, int cost) {
        this->to = to;
        this->cost = cost;
    }
};
vector<Edge> G[MAX_N];
typedef pair<int, int> P;

void dijkstra(int s) {
    fill(d[s], d[s] + MAX_N, INF);
    d[s][s] = 0;
    priority_queue<P, vector<P>, greater<P> > que;
    que.push(P(0, s));
    while (que.size()) {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (p.first > d[s][v]) {
            continue;
        }
        for (int i = 0; i < G[v].size(); i++) {
            Edge e = G[v][i];
            if (p.first + e.cost < d[s][e.to]) {
                d[s][e.to] = p.first + e.cost;
                que.push(P(d[s][e.to], e.to));
            }
        }
    }
}

int main() {
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif    
    scanf("%d %d %d", &N, &M, &X);
    int from, to, cost;
    for (int i = 0; i < M; i++) {
        scanf("%d %d %d", &from, &to, &cost);
        G[from].push_back(Edge(to, cost));
    }

    int total[MAX_N];
    for (int i = 1; i <= N; i++) {
        dijkstra(i);
    }
    for (int i = 1; i <= N; i++) {
        total[i] = d[i][X] + d[X][i];
    }
    printf("%d", *max_element(total + 1, total + N + 1));
    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    return 0;
}
```

&nbsp;