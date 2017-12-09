---
title: POJ 3187 Backward Digit Sums 题解
id: 98
date: 2017-02-12 21:53:38
tags:
---

<span style="color: #008000;">[POJ 3187 Backward Digit Sums ](http://poj.org/problem?id=3187)</span>

思路：

这道题很简单，用next_permutation枚举1~N的所有排列，然后依次相加，判断最后的和是否等于sum，是的话则break，即为字典序最前的。
```
#include <iostream>
#include <algorithm>

using namespace std;

int n;
int sum;
int mycase[11][11];

int main() {
    cin &gt;&gt; n &gt;&gt; sum;
    for (int i = 1; i &lt;= n; i++) {
        mycase[n][i] = i;
    } 
    do {
        for (int i = n; i &gt; 1; i--) {
            for (int j = 1; j &lt; i; j++) {
                mycase[i - 1][j] = mycase[i][j] + mycase[i][j + 1];
            }
        }
        if (mycase[1][1] == sum) {
            for (int i = 1; i &lt;= n; i++) {
                cout &lt;&lt; mycase[n][i] &lt;&lt; " ";
            }
            break;
        }
    } while (next_permutation(mycase[n] + 1, mycase[n] + 1 + n));
    return 0;
}
```

注意：

*   next_permutaiton(m, n)的两个参数分别是枚举起始的元素，和结束的元素的后一个元素。也就是区间为[m, n).