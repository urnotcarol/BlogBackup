---
title: POJ 3253 Fence Repair 题解
id: 108
date: 2017-02-12 22:09:03
tags:
---

[POJ 3253 Fence Repair ](https://vjudge.net/problem/15032/origin)

书中例题。

在看题解之前自己做了一下，用了错误的贪心策略：“越短的木棒切割的时间应该越晚”，局部来看是对的。

正确策略的局部也是这样的。

不过自己的想法不够全面，只想到了第一步。

例如需要1,2,3,4,5 五根木棒，我觉得应该：

先花15的代价切下来长度为5的，

再花10的代价切下来长度为4的，

再花6的代价切下来长度为3的，

最后花3的代价切下来长度为2的，

剩下的也就是长度为1的。

这样代价一共是15+10+6+3=34，## 而最优策略代价为33.

因此， 虽然说越长的木棒应该最先被切没错，但是对于选择最长木棒这件事，并不是直接从题目所需要的所有长度的木棒里面选最长的，而是：## 两根次长的也许可以拼成一根比当前最长更长的。

## 因此应该从小往大遍历，不断选取最短的两根木棒组成新木棒加入所需队列中，换个角度就是说：最短的木棒与次短的木棒应该是兄弟节点。

&nbsp;

仔细领会错误和正确策略的区别（懒得开Visio了✧(≖ ◡ ≖✿)）：
1+2拼成3，3+3拼成6，此时需要6、5、4三根木棒，6便是最大的，而不是从一开始受限的角度所得出的5。
![null](http://images2015.cnblogs.com/blog/948557/201702/948557-20170208155223213-748890318.png)
&nbsp;
```
#include <iostream>
#include <algorithm>

using namespace std;

int n;
int plank[20000];

void slove() {
    long long res = 0;

    while (n &gt; 1) {
        int min = 0;        //最小的板的下标 
        int minNext = 1;    //次小的板的下标 

        if (plank[min] &gt; plank[minNext]) {
            swap(min, minNext);
        }

        for (int i = 2; i &lt; n; i++) {           //找到最小和次小的两块板 
            if (plank[i] &lt; plank[min]) {
                minNext = min;
                min = i;
            }
            else if (plank[i] &lt; plank[minNext]) {
                minNext = i;
            }
        }

        int t = plank[min] + plank[minNext];
        res += t;

        if (min == n - 1) {                //将拼合后的板加入到数组中，去掉原来的 
            swap(min, minNext); 
        }
        plank[min] = t;
        plank[minNext] = plank[n - 1];

        n--;         
    }
    cout &lt;&lt; res &lt;&lt; endl;
}

int main() {
    cin &gt;&gt; n;
    for (int i = 0; i &lt; n; i++) {
        cin &gt;&gt; plank[i];
    }
    slove();

    return 0;    
}
```

&nbsp;