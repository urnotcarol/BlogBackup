---
title: POJ 2236 Wireless Network 题解
id: 233
date: 2017-03-15 22:20:06
tags:
- 算法
- OJ
categories:
- 算法
- 并查集
---

[POJ 2236 Wireless Network](http://poj.org/problem?id=2236)

## 题目大意

程序员要修电脑。N 台电脑位于不同的坐标，距离 d 内且被修好的电脑可以互联。给定每台电脑的坐标（coordinate，刚开始不认识这个词），然后开始修，修着修着检测一下 p，q 两台能不能互联。求每次检测的结果。

## 思路

互联的先决条件是距离近，因此刚开始用数组 reach[i][j] 保存每两台电脑是否够近。接下来每次修好一台电脑，就将其与所有方圆 d 米内且已经修好的电脑加入并查集的同一小组。检测的时候检测是否为同一小组就好了。

## AC代码
```
#include <iostream>
#include <stdio.h>
#include <math.h>
#include <memory.h>

using namespace std;

const int MAX_N = 1001;
int N;
int d;
int x[MAX_N + 4];
int y[MAX_N + 4];
bool repaired[MAX_N + 4];
bool reach[MAX_N +4][MAX_N + 4];

int par[MAX_N + 4];
int rank[MAX_N + 4];

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

int main() {
    scanf("%d %d", &N, &d);
    init(N + 1);
    for (int i = 1; i <= N; i++) {
        scanf("%d %d", &x[i], &y[i]);
    }
    for (int i = 1; i <= N; i++) {
        for (int j = i + 1; j <= N; j++) {
            if (sqrt((double)((x[i] - x[j]) * (x[i] - x[j]) + (y[i] - y[j]) * (y[i] - y[j]))) <= d) {
                reach[i][j] = true;
                reach[j][i] = true;
            }
        }
    }
    cin.ignore();
    char operation;
    while (scanf("%c", &operation) != EOF) {
        if (operation == 'O') {
            int x;
            scanf("%d", &x);
            repaired[x] = true;
            for (int i = 1; i <= N; i++) {
                if (i == x) {
                    continue;
                }

                if (reach[x][i] && repaired[i]) {
                    unite(i, x);                    
                }
            }            
        }
        else if (operation == 'S') {
            int p;
            int q;
            scanf("%d %d", &p, &q);
            if (same(p, q)) {
                printf("SUCCESS\n");
            } 
            else {
                printf("FAIL\n");
            }                      
        }
        cin.ignore();
    }
    return 0;
}
```

