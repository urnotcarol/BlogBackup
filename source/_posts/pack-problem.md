---
title: 背包问题 HDU 2602 Bone Collector + HDU 1114 Piggy-Bank
id: 148
date: 2017-02-19 22:29:15
tags:
---


HDU 2602 Bone Collector


最简单的01背包问题
```\n#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <memory.h>

using namespace std;

const int MAX_N = 1000;

int N;
int V;
int value[MAX_N + 1];
int vol[MAX_N + 1];
long long dp[MAX_N + 1];

int main() {
    int input;
    cin &gt;&gt; input;
    while(input-- &gt; 0) {
        cin &gt;&gt; N &gt;&gt; V;
        for (int i = 0; i &lt; N; i++) {
            scanf("%d", &amp;value[i]);
        }
        for (int i = 0; i &lt; N; i++) {
            scanf("%d", &amp;vol[i]);
        }

        for (int i = 0; i &lt; N; i++) {
            for (int j = V; j &gt;= vol[i]; j--) {
                dp[j] = max(dp[j], dp[j - vol[i]] + value[i]);
            }
        }
        printf("%lld\n", dp[V]);
        memset(dp, 0, sizeof(dp));
    }
    return 0;
}
```


[HDU 1114 Piggy-Bank](http://acm.hdu.edu.cn/showproblem.php?pid=1114)


完全背包：包中的每个物品有无限多个
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <memory.h>

using namespace std;

const int MAX_N = 500;
const int MAX_E = 10000;
const int INF = 0x3f3f3f3f;
int T;
int E;
int F;
int N;
pair<int, int> coin[MAX_N + 3];
int dp[MAX_E + 3];

int main() {
    scanf("%d", &amp;T);
    while(T--) {
        scanf("%d %d", &amp;E, &amp;F);
        scanf("%d", &amp;N);
        for (int i = 0; i &lt; N; i++) {
            scanf("%d %d", &amp;coin[i].first, &amp;coin[i].second);
        }
        E = F - E;
        memset(dp, INF, sizeof(dp));
        dp[0] = 0;
        for (int i = 0; i &lt; N; i++) {
            for (int j = coin[i].second; j &lt;= E; j++) {
                dp[j] = min(dp[j], dp[j - coin[i].second] + coin[i].first);
            }
        }
        if (dp[E] != INF) {
            printf("The minimum amount of money in the piggy-bank is %d.\n", dp[E]);
        }
        else {
            printf("This is impossible.\n");
        }
    }
    return 0;
}
```

&nbsp;
> &nbsp;
> 
> 我们看到的求最优解的背包问题题目中，事实上有两种不太相同的问法。有的题目要求“恰好装满背包”时的最优解，有的题目则并没有要求必须把背包装满。一种区别这两种问法的实现方法是在初始化的时候有所不同。
> 
> 如果是第一种问法，要求恰好装满背包，那么在初始化时除了F[0]为0，其 它F[1..V ]均设为−∞，这样就可以保证最终得到的F[V ]是一种恰好装满背包的最优解。 如果并没有要求必须把背包装满，而是只希望价格尽量大，初始化时应该将F[0..V ]全部设为0。 这是为什么呢？可以这样理解：初始化的F数组事实上就是在没有任何物品可以放入背包时的合法状态。如果要求背包恰好装满，那么此时只有容量 为0的背包可以在什么也不装且价值为0的情况下被“恰好装满”，其它容量的背包均没有合法的解，属于未定义的状态，应该被赋值为-∞了。如果背包并非必须被装满，那么任何容量的背包都有一个合法解“什么都不装”，这个解的价值为0，所以初始时状态的值也就全部为0了。  ——背包九讲
开始不太理解这段话，自己用纸笔画表推了一遍就懂了。一定不能怕麻烦。

常见的背包要求得到最大的价值，而像上面的 HDU1114 这道题求的是最小的价值。针对这种要求，除了把 max 换成 min 之外，dp 数组初始化也不再用 −∞，而是用 +∞。


[HDU 2192 512](http://acm.hdu.edu.cn/showproblem.php?pid=2191)


多重背包，即每个物品的数量有限。

测试好多数据都对，一直 WA，没有找到原因，暂作阙疑吧……
```\n#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <memory.h>

const int MAX_N = 100;
int C;
int n;
int m;
int p;
int h;
int c;
long long dp[MAX_N + 5];

using namespace std;

int main() {
    scanf("%d", &amp;C);
    while(C--) {
        memset(dp, 0, sizeof(dp));
        scanf("%d %d", &amp;n, &amp;m);
        for (int i = 0; i &lt; m; i++) {
            scanf("%d %d %d", &amp;p, &amp;h, &amp;c);
            if (p * c &gt;= n) {
                for (int j = p; j &lt;= n; j++) {
                    dp[j] = max(dp[j], dp[j - p] + h);
                }
            }
            else {
                int k = 1;
                while (k &lt; c) {
                    for (int j = n; j &gt;= p; j--) {
                        dp[j] = max(dp[j], dp[j - k * p] + k * h);
                    }
                    c -= k;
                    k *= 2;
                }                
                for (int j = n; j &gt;= p; j--) {
                    dp[j] = max(dp[j], dp[j - c * p] + c * h);
                }

            }
        }
        cout &lt;&lt; dp[n] &lt;&lt; endl;
    }
    return 0;
}
```

要点：

01背包，节省空间用一维数组的话，关键在于**逆序**。

完全背包，用一维数组，关键在于**正序**。

多重背包简化成 01 背包和完全背包。

&nbsp;

代码中可以简化的地方：

每个物品的重量、价值、数量这三个变量其实都可以在每个最外层循环中重复利用，不必设置多个数组，这样好写也好记。

&nbsp;

&nbsp;

reference

* * *


[背包问题九讲](http://cuitianyi.com/blog/%E3%80%8A%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98%E4%B9%9D%E8%AE%B2%E3%80%8B2-0-alpha1/)
