---
title: POJ 2229 Sumsets 题解
id: 145
date: 2017-02-22 13:27:59
tags:
---


[POJ 2229 Sumsets](http://poj.org/problem?id=2229)


思路：

两种方法。

方法一：根据题目特定的条件找出规律，用小于n的2的幂组成n，令 dp[i] 表示方案总数，有下面两种情况：

*   i为奇数，dp[i] = dp[i - 1]
*   i为偶数，有两种可能，一种是组成 i 的数字里面没有 1, 即全是偶数，此时方案数为 dp[i / 2], 另一种可能是有 1, 那么必须有两个1，剩下的就是 dp[i - 2]. 故 dp[i] = dp[i - 1] + dp[i&gt;&gt;1]
```
#include <iostream>
#include <stdio.h>
#include <math.h>

using namespace std;

const int MAX_N = 1000000;
const int MOD = 1000000000;
int n;
int dp[MAX_N + 3];

int main() {
    cin &gt;&gt; n;
    dp[0] = 1;
    dp[1] = 1;
    for (int j = 2; j &lt;= n; j++) {
        if (j &amp; 1) {
            dp[j] = dp[j - 1] % MOD;
        }
        else {
            dp[j] = (dp[j - 2] + dp[j &gt;&gt; 1]) % MOD;
        }

    }
    cout &lt;&lt; dp[n];
    return 0;
}
```

&nbsp;

方法二：看成完全背包问题，令 dp[i][j] 表示前 i 个数字组成 j 的方案数。 由求最大价值变成求方案总数。方程里面的 min 改成 sum。

这里重复利用 dp 数组，因此可简化成一维数组。
```
#include <iostream>
#include <stdio.h>
#include <math.h>

using namespace std;

const int MAX_N = 1000000;
int n;
int number[20];
int dp[MAX_N + 3];

int main() {
    for (int i = 0; i &lt; 20; i++) {
        number[i] = 1 &lt;&lt; i;
    }
    cin &gt;&gt; n;
    int m = log((float)n) / log(2.0);
    dp[0] = 1;
    for (int i = 0; i &lt;= m; i++) {
        for (int j = number[i]; j &lt;= n; j++) {
            dp[j] = (dp[j] + dp[j - number[i]]) % 1000000000;
        }
    }
    cout &lt;&lt; dp[n];
    return 0;
}
```

总结：第一种方法更有技巧性，只有一层循环。充分利用了题目的数据特点（都是 2 的幂），但是只适用于这一种情况。

第二种方法更通用，没有考虑数据特点，因此也适用于其他的没有特定规律的数据，比如1, 2, 5, 10...

&nbsp;