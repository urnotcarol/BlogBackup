---
title: AOJ 0189 Convenient Location 题解
id: 269
date: 2017-03-31 11:24:18
tags:
---

[AOJ 0189 Convenient Location](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=0189)

## 题意

如图所示，有若干个房子，给出一些边，权重代表两个房子间距离长度，求出哪个房子到其他所有房子的距离最短。

![](https://odzkskevi.qnssl.com/42d8d42d2db84e144530c2c3a16e1f5d?v=1490930000)

## 思路

求任意两点间最短距离，我用了 Bellman-Ford，事实上用 Floyd 算法更好。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>

using namespace std;

const int MAX_N = 45 * 2 + 4;
const int MAX_V = 10 + 4;
const int INF = 0x3f3f3f3f;

int n;
int v;
struct Edge {
    int from;
    int to;
    int cost;
} edge[MAX_N];

int d[MAX_V];

int Bellman_Ford (int s) {
    int res = 0;
    fill(d, d + MAX_V, INF);
    d[s] = 0;
    while (true) {
        bool update = false;
        for (int i = 0; i &lt; 2 * n; i++) {
            Edge e = edge[i];
            if (d[e.from] != INF &amp;&amp; d[e.from] + e.cost &lt; d[e.to]) {
                d[e.to] = d[e.from] + e.cost;
                update = true;
            }
        }
        if (!update) {
            break;
        }        
    }
    for (int i = 0; i &lt;= v; i++) {
        res += d[i];
    }
    return res;
}

int main() {
    while (scanf("%d", &amp;n) &amp;&amp; n &gt; 0) {
        int path[MAX_V];                           
        v = 0;
        for (int i = 0; i &lt; n; i++) {
            scanf("%d %d %d", &amp;edge[i].from, &amp;edge[i].to, &amp;edge[i].cost);
            edge[i + n].from = edge[i].to;
            edge[i + n].to = edge[i].from;
            edge[i + n].cost = edge[i].cost;
            v = max(v, edge[i].from);
            v = max(v, edge[i].to);
        }

        for (int i = 0; i &lt;= v; i++) {
            path[i] = Bellman_Ford(i);
        }
        printf("%d %d\n", min_element(path, path + v + 1) - path, *min_element(path, path + v + 1));
    }
    return 0;    
}
```

