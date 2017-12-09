---
title: AOJ 2170 Marked Ancestor 题解
id: 240
date: 2017-03-18 21:36:00
tags:
---

[AOJ 2170 Marked Ancestor](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2170)

## 题目大意

给定一棵树，开始只有根节点被标记，其他结点都未被标记。有两种操作，一种是标记某个结点，另一种是查距离某个结点最近的祖先结点（ancestor）。输出每次查询所得结果的和。

## 思路

设置一个标记变量 marked，结点 i 被标记后就将 marked[i] 置 1。查询时用并查集里的 find 操作寻找其祖先结点。

<span style="color: #ff0000;">**注意**</span>

一个结点的祖先结点包括其自身，子孙结点也包括其自身~~~

## AC代码
```
#include <iostream>
#include <stdio.h>
#include <memory.h>

using namespace std;

const int MAX_N = 100000;
int N;
int Q;
bool marked[MAX_N + 4];
int par[MAX_N + 4];

int find(int x) {
    if (marked[x] == true) {
        return x;
    }
    return find(par[x]);
}

int main() {
    while (scanf("%d %d", &amp;N, &amp;Q) != EOF &amp;&amp; N &gt; 0 &amp;&amp; Q &gt; 0) {
        memset(marked, 0, sizeof(marked));
        marked[1] = 1;
        long long res = 0;
        for (int i = 2; i &lt;= N; i++) {
            scanf("%d", &amp;par[i]);
        }
        par[1] = 1;
        cin.ignore();
        char operation;
        int node;
        for (int i = 0; i &lt; Q; i++) {
            scanf("%c %d", &amp;operation, &amp;node);
            if (operation == 'Q') {
                res += find(node);
            }
            else if (operation == 'M') {
                marked[node] = true;
            }
            cin.ignore();
        }
        cout &lt;&lt; res &lt;&lt; endl;
    }
    return 0;
}
```

