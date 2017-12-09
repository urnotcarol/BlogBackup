---
title: POJ 2385 Apple Catching 题解
id: 152
date: 2017-03-02 16:13:44
tags:
---


[POJ 2385 Apple Catching](http://poj.org/problem?id=2385)


### 注意


奶牛刚开始站在 #1 树下；步数不要求必须用完。

### 思路


令 dp[i][j] 表示 前 i 秒**用完** j 步时，最多可以接到的苹果的个数。

状态转移方程为：dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - 1]) + (a[i] == j % 2 + 1), 其中 a[i] 是输入的数组。

方程解释：“前 i 秒用完 j 步”这个子问题，若第 i 秒站在原地未动，说明“前 i - 1 秒用完了 j 步”，即为 dp[i - 1][j]；若第 i 秒时刚从另一棵树下移动过来，说明“前 i - 1 秒用完了 j - 1 步”，即为 dp[i - 1][j - 1]。两者之间取更大值，便得到前 i - 1 秒接到的苹果的最大值 T =  max(dp[i - 1][j], dp[i - 1][j - 1])。再看第 i 秒可否接到苹果。因为设定是**用完** j 步，即无论如何奶牛都移动了 j 次，所以这时 j 的奇偶性就可以说明奶牛站在#1， #2 哪棵树下，再判断此时这棵树会不会落下苹果（ a[i] 的值为 1 或 2），就可以决定 T 是否要加 1，便得到 dp[i][j]。

需要注意的是，在循环结束后，dp[T][W] 并不一定是前 T 秒接到苹果的最大值，因为限定了步数用完，所以用完步数反而可能导致接到的苹果变少，如下组数据：
> 输入
> 
> 1 1
> 
> 1
> 
> 输出
> 
> 0
因为必须用完 1 步，所以奶牛在 1 秒时会移到 #2 树下，没接到这个苹果，dp[1][1] 为 0，而正确输出应该为 1，即没有动的时候 dp[1][0]。

因此最后应输出 dp[T][0] ~ dp[T][W] 中的最大值。

&nbsp;

<span style="font-size: 14pt;">AC代码</span>
```
#include <iostream>
#include <stdio.h>
#include <math.h>

using namespace std;

const int MAX_T = 1000;
const int MAX_W = 30;
int n;
int T;
int W;
int dp[MAX_T + 3][MAX_W + 3];
int apple[MAX_T + 3];

int main() {
    cin &gt;&gt; T &gt;&gt; W;
    for (int i = 1; i &lt;= T; i++) {
        scanf("%d", &amp;apple[i]);
        dp[i][0] = dp[i - 1][0] + (apple[i] == 1);
        for (int j = 1; j &lt;= W; j++) {
            dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - 1]) + (apple[i] == j % 2 + 1);
        }
    }

    int maxA = 0;
    for (int i = 0; i &lt;= W; i++) {
        maxA = max(maxA, dp[T][i]);
    }
    cout &lt;&lt; maxA;
    return 0;
}
```

&nbsp;