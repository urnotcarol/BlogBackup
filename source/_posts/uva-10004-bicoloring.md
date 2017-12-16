---
title: UVA 10004 Bicoloring 题解
id: 248
date: 2017-03-18 21:53:49
tags:
- 算法
- OJ
categories:
- 算法
- 图论基础
---

[UVA 10004 Bicoloring](https://vjudge.net/problem/UVA-10004)

## 题意

给定一张图，求是否能二分着色。

## 思路

纯基础的二分图问题，本来是因为上道题一直 WA 才找了这道题来写的。写着写着果然就明白 bug 在哪里了。上道题没有及时 return，导致如果有非连通图存在的话，只要有一个图可以着色就会返回 true，而实际上应该是 false.

## AC代码

// 这种写法不用考虑是否是连通图的问题。都可以得出正确结果。
```
#include <iostream>
#include <vector>
#include <stdio.h>
#include <memory.h>

using namespace std;

const int MAX_N = 200 + 4;
int n;
int l;

vector<int> G[MAX_N];
int color[MAX_N];

bool dfs(int v, int c) {
    color[v] = c;
    for (int i = 0; i < G[v].size(); i++) {
        if (color[G[v][i]] == c) {
            return false;
        }
        if (color[G[v][i]] == 0 && !dfs(G[v][i], -c)) {
            return false;
        }
    }
    return true;
}

bool solve() {
    for (int i = 0; i < n; i++) {
        if (color[i] == 0) {
            if (!dfs(i, 1)) {
                return false;
            }
        }
    }
    return true;
}

int main () {
    while(scanf("%d", &n) && n > 0) {
        for (int i = 0; i < MAX_N; i++) {
            G[i].clear();
        }
        memset(color, 0, sizeof(color));
        scanf("%d", &l);
        int a;
        int b;
        for (int i = 0; i < l; i++) {
            scanf("%d %d", &a, &b);
            G[a].push_back(b);
            G[b].push_back(a);
        }
        if (solve()) {
            printf("BICOLORABLE.\n");
        }
        else {
            printf("NOT BICOLORABLE.\n");
        }        
    }
    return 0;
}
```

