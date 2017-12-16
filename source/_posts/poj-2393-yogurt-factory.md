---
title: POJ 2393 Yogurt factory 题解
id: 117
date: 2017-02-12 22:17:15
tags:
- 算法
- OJ
categories:
- 算法
- 动态规划
---

[POJ 2393 Yogurt factory](https://vjudge.net/problem/16489/origin)

题目很简单，有一些需要注意的地方。

刚开始没有定义成long long的变量导致WA。

O(n<sup>2</sup>)的复杂度导致TLE。

学习@[Lorazepam ](http://www.cnblogs.com/oscar-cnblogs/p/6360254.html)，用了动态规划。

```
#include <iostream>
#include <stdio.h>
#include <algorithm>

using namespace std;

typedef long long ll;
int n;
int s;
ll c[10005];
ll y[10005];

void solve() {
    ll res = 0;

    for (int i = 1; i < n; i++) {
        c[i] = min(c[i], c[i - 1] + s);  //每一周只需要和上周比较的原因是上周和上上周也比较过了。
    } 
    for (int i = 0; i < n; i++) {
        res += c[i] * y[i];
    }

    printf("%lld\n", res);
}

int main() {
    scanf("%d %d", &n, &s);
    for (int i = 0; i < n; i++) {
        scanf("%d %d", &c[i], &y[i]);
    }
    solve();
    return 0;
}
```

&nbsp;

