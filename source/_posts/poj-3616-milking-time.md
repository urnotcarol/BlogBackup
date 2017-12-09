---
title: POJ 3616 Milking Time 题解
id: 153
date: 2017-03-03 16:29:39
tags:
---


[POJ 3616 Milking Time](http://poj.org/problem?id=3616)


## 思路

对区间排序。令 dp[i] 表示到第 i 个区间时，取到的牛奶的最大值。dp[i] 为当前效率 + 之前所有能与之兼容的区间里取奶的最大值。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>

using namespace std;

const int MAX_M = 1000;
int N;
int M;
int R;
struct Invl {
    int s;
    int t;
    int eff;
    bool operator &lt; (Invl &amp; i) {
        return s &lt; i.s;
    }
};
Invl invl[MAX_M + 3];
int dp[MAX_M + 3];

int main() {
    scanf("%d %d %d", &amp;N, &amp;M, &amp;R);
    for (int i = 1; i &lt;= M; i++) {
        scanf("%d %d %d", &amp;invl[i].s, &amp;invl[i].t, &amp;invl[i].eff);
    }
    sort(invl + 1, invl + 1 + M);
    for (int i = 1; i &lt;= M; i++) {
        dp[i] = invl[i].eff;
        for (int j = 1; j &lt;= i; j++) {
            if (invl[j].t + R &lt;= invl[i].s) {
                dp[i] = max(dp[i], dp[j] + invl[i].eff); 
            }            
        }
    }
    cout &lt;&lt; *max_element(dp + 1, dp + 1 + M);
    return 0;
}
```

## 反思

刚开始用了 01 背包的思路，用 dp[i][j] 表示前 i 个区间直到 j 时刻取奶得到的最大值。有 O(N * M) 的复杂度所以 TLE 了_(:зゝ∠)_ 保存一下代码……
```
#include <iostream>
#include <stdio.h>
#include <algorithm>

using namespace std;

const int MAX_N = 1000000;
const int MAX_M = 1000;
int N;
int M;
int R;
long long dp[MAX_N + 3];
pair<pair&lt;int, int&gt;, int> invl[MAX_M + 3];

int main() {
    scanf("%d %d %d", &amp;N, &amp;M, &amp;R);
    for (int i = 1; i &lt;= M; i++) {
        scanf("%d %d %d", &amp;invl[i].first.first, &amp;invl[i].first.second, &amp;invl[i].second);
    }
    sort(invl + 1, invl + 1 + M);
    for (int i = 1; i &lt;= M; i++) {
        for (int j = N; j &gt;= invl[i].first.second; j--) {             
            dp[j] = max(dp[j], dp[(invl[i].first.first - R) * (invl[i].first.first - R &gt; 0)] + invl[i].second);
        }
    }
    cout &lt;&lt; dp[N];
    return 0;
}
```

这两种思路看似只有内层循环不一样，实际上最大的区别在于 dp 的对象不同。第一种在于第 i 个区间，而第二种在于第 i 个时刻。第一种的 dp[i] 表示的数值一定取了当前区间内的牛奶，而第二种的 dp[i][j] 只是表示算到这儿了，不一定取了这个。也是由于这个原因，第一种最后要输出 dp 数组中的最大值，而第二种输出 dp 数组最末的一个值。

## extra

*max_element(first, last) 函数，和 sort() 函数类似，取 [first, last) 区间内的最大值，在 <algorithm> 里面，不用自己写循环了。