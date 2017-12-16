---
title: POJ 1328 Radar Installation 题解
id: 113
date: 2017-02-12 22:14:01
tags:
- 算法
- OJ
categories:
- 算法
- 贪心
---

[POJ 1328 Radar Installation](https://vjudge.net/problem/10537/origin)

第一次的思路：所有岛屿按x坐标排序，从最左开始，画圆心在x轴上的圆，使得该岛屿在圆的边上，也就是选择圆心尽量靠右的圆。再从第一个没有被该圆覆盖的岛屿开始上述过程。

错误，因为岛屿还有y坐标的影响，会出现这种情况：岛屿i被一个圆覆盖了，但是岛屿i-1位置更高，没有被覆盖。

正确策略：以岛屿为圆心做圆，与x轴所成的弦，就是可以覆盖这个岛屿的雷达的区间。将所有区间去重。

区间去重的时候要注意：是否会影响当前用来作比较的数，比如例题POJ3069就不会，而这道题会，雷达的位置是变化的，要处在所有区间的交集内：

`rx = min(rx, r[i].second);`

```
#include <iostream>
#include <math.h>
#include <algorithm>

using namespace std;

int n;
int d;
pair<int, int> p[1000];
pair<double, double> r[1000];

double getDx(int py) {
    return (double)sqrt((double)(d * d - py * py));
}

int solve() {
    int res = 0;
    for (int i = 0; i < n; i++) {
        r[i].first = p[i].first - getDx(p[i].second);
        r[i].second = p[i].first + getDx(p[i].second);
    }
    sort(r, r + n);
    int i = 0;
    while (i < n) {
        double rx = r[i].second;
        i++;
        while (i < n && (double)(r[i].first) <= rx) {
            rx = min(rx, r[i].second);
            i++;
        }
        res++;
    }
    return res;
}

int main() {
    int caseNO = 0;
    while(cin >> n >> d && n!= 0) {
        caseNO++;
        int solvable = 1;
        for (int i = 0; i < n; i++) {
            cin >> p[i].first;
            cin >> p[i].second;
            if (p[i].second > d) {
                solvable = 0;
            }
        }
        if (solvable) {
            cout << "Case " << caseNO << ": " << solve() << endl;
        }
        else {
            cout << "Case " << caseNO << ": -1" << endl;
        }   
    }
    return 0;    
}
```
