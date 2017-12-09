---
title: 'POJ 1703 Find them, Catch them 题解'
id: 238
date: 2017-03-18 11:35:40
tags:
---

[POJ Find them, Catch them](http://poj.org/problem?id=1703)

## 题目大意

有两拨坏人，龙帮和蛇帮，干了很多坏事。现在只知道某两件坏事不是同一个帮干的。先给一些描述，最后查询某两件事是否是同一帮干的。

## 思路

食物链的简化版。2 * N 个元素，1 - N 表示属于龙帮，N + 1 到 2N 表示属于蛇帮。D 操作的时候将相应的编号加入并查集中的一个组， A操作的时候查询是否属于同一个组。

## AC代码
```
#include <iostream>
#include <stdio.h>

using namespace std;

const int MAX_N = 100000;
int T;
int N;
int M;

int par[MAX_N * 2 + 4];
int rank[MAX_N * 2 + 4];

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
    if (rank[x] &lt; rank[y]) {
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
    scanf("%d", &amp;T);
    while (T--) {
        scanf("%d %d", &amp;N, &amp;M);
        init(N * 2);
        char message;
        int a;
        int b;
        cin.ignore();
        for (int i = 0; i &lt; M; i++) {
            scanf("%c %d %d", &amp;message, &amp;a, &amp;b);
            a--;
            b--;
            if (message == 'D') {
                unite(a, b + N);
                unite(a + N, b);
            }
            else {
                if (same(a, b)) {
                    printf("In the same gang. \n");
                }
                else if (same(a, b + N)) {
                    printf("In different gangs.\n");
                }
                else {
                    printf("Not sure yet.\n");
                }                
            }
            cin.ignore();
        }
    }
    return 0;
}
```

