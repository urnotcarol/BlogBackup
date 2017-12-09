---
title: POJ 3190 Stall Reservations 题解
id: 115
date: 2017-02-12 22:15:34
tags:
---

[POJ 3190 Stall Reservations](https://vjudge.net/problem/39459/origin)

思路：

优先选择进食最早的奶牛，晚来的奶牛如果进食时间和前一只奶牛重叠，就放到一个新栏里，否则的话就放在当前栏里。
```
#include <iostream>
#include <algorithm>
#include <stdio.h>

using namespace std;

int n;
struct cow {
    int first;
    int second;
    int index;
    bool operator &lt; (const cow &amp;c) {
        return first &lt; c.first;
    }
};
cow invl[50000];
pair<int, int> stall[50001];
int assigned[50000];

void solve() {
    int res = 1;
    sort(invl, invl + n);
    for (int i = 0; i &lt; n; i++) {
        bool hasFound = 0;
        int s = invl[i].first;      
        int t = invl[i].second;
        int cowIndex = invl[i].index;

        for (int j = 1; j &lt;= res; j++) {            
            if (s &gt; stall[j].second) {
                stall[j].second = t;
                hasFound = 1;
                assigned[cowIndex] = j;
                break;
            }
        }

        if (hasFound == 0) {
            res++;
            stall[res].first = s;
            stall[res].second = t;
            assigned[cowIndex] = res;
        }              
    }
    printf("%d\n", res);
    for (int i = 0; i &lt; n; i++) {
        printf("%d\n", assigned[i]);
    } 
}

int main() {
    cin &gt;&gt; n;
    for (int i = 0; i &lt; n; i++) {
        scanf("%d", &amp;invl[i].first);
        scanf("%d", &amp;invl[i].second);
        invl[i].index = i;
    }
    solve();
    return 0;
}
```

&nbsp;