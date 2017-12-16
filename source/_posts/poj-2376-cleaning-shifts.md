---
title: POJ 2376 Cleaning Shifts 题解
id: 110
date: 2017-02-12 22:11:12
tags:
- 算法
- OJ
categories:
- 算法
- 贪心
---

[POJ 2376 Cleaning Shifts](https://vjudge.net/problem/10046/origin)

思路：

所有可工作的区间左端点进行排序，如果没有从1开始的，直接输出-1；

否则，从最早的区间开始，寻找左端点在这个区间之内，同时右端点大于这个区间右端点的区间，如果找不到，说明中间断掉，也输出-1；

中间没有断掉的话，就不断循环上述过程，直到当前工作结束时间大于shifts的最大值。
```
#include <iostream>
#include <algorithm>
#include <stdio.h>

using namespace std;

int n;
int t;
pair<int, int> invl[25000];

void solve() {
    sort(invl, invl + n);
    int res = 0;
    int cur = 0;

    while (cur < t) {
        int maxT = 0;
        int count = 0;

        for (int i = 0; i < n; i++) {
            if (invl[i].first <= cur + 1 && invl[i].second > cur) {
                count++;
                maxT = max(maxT, invl[i].second);
            }
        }

        if (count == 0) {
            printf("-1");
            return;
        }
        cur = maxT;
        res++;
    }

    printf("%d", res);
}

int main() {
    scanf("%d %d", &n, &t);
    int maxT = 0;
    for (int i = 0; i < n; i++) {
        scanf("%d %d", &invl[i].first, &invl[i].second);
        maxT = max(maxT, invl[i].second);
    }
    if (maxT < t) {
        printf("-1");
    } 
    else {
        solve();
    }
    return 0;    
}
```

&nbsp;