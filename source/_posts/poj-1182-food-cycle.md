---
title: POJ 1182 食物链 题解
id: 230
date: 2017-03-15 22:13:00
tags:
---

[ POJ 1182 食物链](http://poj.org/problem?id=1182)

## 题目大意

动物王国中有三类动物A,B,C，这三类动物的食物链构成了有趣的环形。A吃B， B吃C，C吃A。
现有N个动物，以1－N编号。每个动物都是A,B,C中的一种，但是我们并不知道它到底是哪一种。
有人用两种说法对这N个动物所构成的食物链关系进行描述：
第一种说法是"1 X Y"，表示X和Y是同类。
第二种说法是"2 X Y"，表示X吃Y。
此人对N个动物，用上述两种说法，一句接一句地说出K句话，这K句话有的是真的，有的是假的。当一句话满足下列三条之一时，这句话就是假话，否则就是真话。
1） 当前的话与前面的某些真的话冲突，就是假话；
2） 当前的话中X或Y比N大，就是假话；
3） 当前的话表示X吃X，就是假话。
你的任务是根据给定的N（1 &lt;= N &lt;= 50,000）和K句话（0 &lt;= K &lt;= 100,000），输出假话的总数。

## 思路

并查集这一节中的例题。一上来就是高级应用哇。比普通的求连通否段位高好多的样子。

在这里，并查集中的元素不是动物，而是**事件**，或者说，是命题。用 i, 2i, 3i 分别表示 i 属于 A, i 属于 B, i 属于 C, 这样并查集里每一组表示组内所有元素代表的命题同时发生或不发生。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>

using namespace std;

const int MAX_N = 50000;
const int MAX_K = 100000;
int N;
int K;
int D;
int X;
int Y;

//并查集 
int par[MAX_N * 3 + 4];
int rank[MAX_N * 3 + 4];

//初始化n个元素 
void init(int n) {
    for (int i = 0; i &lt; n; i++) {
        par[i] = i;
        rank[i] = 0;
    }
}

//查询树的根
int find(int x) {
    if (par[x] == x) {
        return x;
    }
    return par[x] = find(par[x]);
} 

//合并x和y所属的集合
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

//判断x和y是否属于同一个集合
bool same(int x, int y) {
    return find(x) == find(y);
} 

int main() {
    scanf("%d %d", &amp;N, &amp;K);
    init(N * 3);
    int ans = 0;
    for (int i = 0; i &lt; K; i++) {
        scanf("%d %d %d", &amp;D, &amp;X, &amp;Y);
        X--;
        Y--;

        if (X < 0 || X &gt;= N || Y &lt; 0 || Y >= N) {
            ans++;   
            continue;
        }

        if (D == 1) {
            if (same(X, Y + N) || same(X, Y + 2 * N)) {
                ans++;
            }
            else {
                unite(X, Y);
                unite(X + N, Y + N);
                unite(X + 2 * N, Y + 2 * N);
            }
        }
        else {
            if (same(X, Y) || same(X, Y +  2 * N)) {
                ans++;
            }
            else {
                unite(X, Y + N);
                unite(X + N, Y + 2 * N);
                unite(X + 2 * N, Y); 
            }
        }
    }
    printf("%d", ans);
    return 0;
}
```

