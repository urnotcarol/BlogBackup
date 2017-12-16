---
title: POJ 1862 Stripies 题解
id: 121
date: 2017-02-12 22:22:27
tags:
- 算法
- OJ
categories:
- 算法
- 贪心
---

[POJ 1862 Stripies](https://vjudge.net/problem/16383/origin)

思路：

贪心策略为每次挑选最大的两个数，进行2*sqrt(a,b)运算后放回到数组中。有点类似于之前做的[Fence Repair](http://www.carolunar.com/2017/02/poj-3253-fence-repair/)题目。

这样可以保证大数被开方的次数更多，最后的结果更小。

注意：

n为1的情况，WA了几次。

学会C++输出格式控制: [http://c.biancheng.net/cpp/biancheng/view/2227.html](http://c.biancheng.net/cpp/biancheng/view/2227.html)。
```
#include <iostream>
#include <stdio.h>
#include <math.h>
#include <iomanip>
#include <algorithm>

using namespace std;

int n;
double w[103];

int main() {
    double res = 0;
    scanf("%d", &n);
    for (int i = 0; i < n; i++) {
        scanf("%lf", &w[i]);
    }
    while (n > 0) {
        if (n == 1) {
            res = w[0];
            break;
        }     

        sort(w, w + n);
        double newW = 2 * sqrt(w[n - 1] * w[n - 2]);
        res = newW;

        w[n - 2] = newW;
        n--;
    }

    cout << setiosflags(ios::fixed) << setprecision(3) << res << endl;
    return 0;
}
```

//这种玄幻的题目读起来有一种读哈利波特的感觉……