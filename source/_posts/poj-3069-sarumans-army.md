---
title: POJ 3069 Saruman's Army 题解
id: 106
date: 2017-02-12 22:05:09
tags:
- 算法
- OJ
categories:
- 算法
- 贪心
---

[POJ 3069 Saruman's Army](http://poj.org/problem?id=3069)

书中例题，关键解法（区间去重）很典型，因此记下来。

> 我们从最左边开始考虑。对于这个点，到距其R以内的区域内必须要有带有标记的点。（此点位于最左边，所以显然）带有标记的这个点一定在此点右侧（包含这个点自身）。
> 
> 于是，究竟要给哪个点加上标记呢？答案应该是从最左边的点开始，距离为R以内的最远的点，因为更左的区域没有覆盖的意义，所以应该尽可能覆盖更靠右的点。
> 
> 如上所示（图），加上了第一个标记后，剩下的部分也用同样的办法处理。对于添加了符号的点右侧相距超过R的下一个点，采用同样的方法找到其右侧R距离以内最远的点添加标记。在所有的点都被覆盖之前不断重复这一过程。
> ```
#include <iostream>
> #include <algorithm>
> 
> using namespace std;
> 
> int r;
> int n;
> int x[1000];
> 
> void solve() {
>     int res = 0;
>     sort(x, x + n);
> 
>     int i = 0;
>     while (i < n) {
>         int s = x[i++];
>         while (i < n && x[i] <= s + r) {
>             i++;
>         }
>         int p = x[i - 1];
>         while (i < n && x[i] <= p + r) {
>             i++;
>         }
>         res++;        
>     }
>     cout << res << endl;
> }
> 
> int main() {
>     while (cin >> r >> n && r != -1) {
>         for (int i = 0; i < n; i++) {
>             cin >> x[i];
>         }
> 
>         solve();
>     }
>     return 0;
> }
```

> &nbsp;