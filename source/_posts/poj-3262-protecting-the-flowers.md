---
title: POJ 3262 Protecting the Flowers 题解
id: 123
date: 2017-02-12 22:25:16
tags:
- 算法
- OJ
categories:
- 算法
- 贪心
---

&nbsp;

[POJ 3262 Protecting the Flowers](http://poj.org/problem?id=3262) 奶牛吃花花

这道题，需要好好总结。

原来的思路是，依次计算出送走奶牛1~N会毁掉的花的数量，找出最小的，送走这头牛。然后又在剩下的奶牛中找可以使毁掉的花的数量最小的送走。如此循环直到全被送走。

对是对，只是也太繁琐了些，并且超时。

没有意识到其实这样的思路进行了很多次重复的比较。

&nbsp;

后来看了[别人](http://www.cnblogs.com/oscar-cnblogs/p/6376273.html)的思路，发现可以先排序，一次就可以排好奶牛应该被送走的顺序。

刚开始觉得，这样很像之前做的Yogurt Factory题目，用动态规划记录每相邻两个牛的比较结果。

写完后发现不对，才发现又陷入了一个误区：动态规划适合于，前面的结果会影响后面的结果，但后面的不会影响前面的结果，的情况。

比如酸奶厂那道题，以后的星期可以用前一个星期生产的成本更低的酸奶，但之前的星期是不可用以后生产的，也就是说是局部比较，并不是统一的排序。

而这道题，需要统一的排序，因此只比较相邻两个是不行的。

&nbsp;

刚开始想到了排序，但没有想到好的排序思路，觉得时长也不能作为依据，吃花数量也不能作为依据，没有想到这两个可以通过运算组成新的依据。
```
#include <iostream>
#include <stdio.h>
#include <algorithm>

using namespace std;

int n;
struct Cow {
    int first;
    int second;
    bool operator < (const Cow & c) {
        return first * c.second < second * c.first;
    }
}; 
Cow cow[100000];
typedef long long ll;

int main() {
    scanf("%d", &n);
    ll sum = 0;
    ll res = 0;
    for (int i = 0; i < n; i++) {
        scanf("%d %d", &cow[i].first, &cow[i].second);
        sum += cow[i].second;   //sum记录所有奶牛吃花的总速度 
    }
    sort(cow, cow + n);
    for (int i = 0; i < n - 1; i++) {
        sum -= cow[i].second;   //每送走一头牛，吃花总速度就减去这头牛的速度 
        res += 2 * sum * cow[i].first;      
    } 
    cout << res << endl;
    return 0;
}
```

&nbsp;