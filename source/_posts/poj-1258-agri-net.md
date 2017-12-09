---
title: POJ 1258 Agri-Net 题解 Prim 最小生成树
id: 305
date: 2017-04-04 19:25:11
tags:
---

[POJ 1258 Agri-Net](http://poj.org/problem?id=1258)

## 题意

FJ 当市长了，他的政治宣言之一就是让所有奶牛都用上互联网。求出连接所有农场的最小花费。

## 思路

求最小生成树，用 Prim 算法。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <vector>
#include <algorithm>
#include <queue>
#define ONLINE_JUDGE

using namespace std;

const int MAX_N = 100 + 4;
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

int prim() {
    fill(minCost, minCost + N, INF);
    fill(used, used + N, false);
    minCost[0] = 0;

    priority_queue<P, vector&lt;P&gt;, greater&lt;P&gt; > que;
    que.push(P(0, 0));

    int res = 0;
    while (!que.empty()) {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (minCost[v] &lt; p.first) {
            continue;
        }
        used[v] = true;
        res += p.first;
        for (int i = 0; i &lt; G[v].size(); i++) {
            Edge e = G[v][i];
            if (!used[e.to] &amp;&amp; minCost[e.to] &gt; e.cost) {
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
    while (1) {
        if (scanf("%d", &amp;N) == EOF) {
            break;
        }
        int cost;
        for (int i = 0; i &lt; N; i++) {
            G[i].clear();
            for (int j = 0; j &lt; N; j++) {
                scanf("%d", &amp;cost);
                if (i != j) {
                    G[i].push_back(Edge(j, cost));
                }
            }
        }
        int res = prim();
        printf("%d\n", res);
    }
    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    return 0;
}
```

<table id="listStatus" class="table table-striped table-bordered table-responsive dataTable no-footer hover-date" width="100%" cellspacing="0">
<tbody>
<tr id="8573524" class="accepted odd">
<td class=" run-id hidden-lg-down"><span style="font-size: 10pt;">8573524</span></td>
<td class=" username">
<div><span style="font-size: 10pt;">[carolunar](https://vjudge.net/user/carolunar)</span></td>
<td class=" oj"><span style="font-size: 10pt;">POJ</span></td>
<td class=" prob_num">
<div><span style="font-size: 10pt;">[1258](https://vjudge.net/problem/POJ-1258)</span></td>
<td class=" status hidden-md-down">
<div class="view-solution" title="" data-toggle="tooltip" data-run-id="8573524" data-original-title=""><span style="font-size: 10pt;">Accepted</span></td>
<td class=" runtime"><span style="font-size: 10pt;">16</span></td>
<td class=" memory"><span style="font-size: 10pt;">0.3</span></td>
<td class=" length hidden-lg-down"><span style="font-size: 10pt;">1671</span></td>
<td class=" language">
<div class="view-solution shared" title="" data-toggle="tooltip" data-html="true" data-run-id="8573524" data-original-title="C++"><span style="font-size: 10pt;">C++</span></td>
<td class=" date">
<div class="localizedTime" data-time="1490601657000"><span class="absolute" style="font-size: 10pt;">2017-03-27 16:00:57</span></td>
</tr>
</tbody>
</table>
&nbsp;