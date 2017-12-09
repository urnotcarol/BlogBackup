---
title: POJ 1065 Wooden Sticks 题解
id: 161
date: 2017-03-10 11:48:18
tags:
---


[POJ 1065 Wooden Sticks](http://poj.org/problem?id=1065)


## 题目大意

有 n 根木棒，每根长度为 l，质量为 w。用一台机器加工这些木棒，刚开始需要 1 分钟的启动时间，接下来，如果每次加工的木棒比前一根长且重的话，就可以不停歇继续加工，否则就需要 1 分钟的清洗时间。给定 n 根木棒的长度和质量，求最短的加工时间。

## 思路

最短加工时间实际上相当于 n 根木棒按长度排序后，质量序列的**最长下降子序列**的长度。或者反过来也可，即对质量排序后，长度序列的 LDS 的长度。

参考最长上升子序列的解决方法，书上 65 页例题所讲。

令 dp[i] 表示长度为 i + 1 的最长下降子序列中末尾元素的最大值（不存在这一长度的 LDS 的话，就是 - 1）。这样做是因为，如果子序列的长度相同，那么末尾元素更大的，在之后会更有优势，可以收更多比它小的当小弟。

就好像要从 100 个顺序固定而高矮不一的人中，挑出尽可能多的人排成一支高度递减的队伍，那么每个人都要和前面所有被挑中的作比较。比如已经选出了一个一米九和一米七的，后面又发现了一米八的小明，那么就把小明作为第二位。处处留心皆学问呀，算法无处不在&gt; &lt;

## AC代码
```
#include <iostream>
#include <stdio.h>
#include <memory.h>
#include <algorithm>
#include <functional>

using namespace std;
const int MAX_N = 5000;

int T;
int n;
pair<int, int> stick[MAX_N + 4];
int dp[MAX_N + 4];

int main() {
    scanf("%d", &amp;T);
    while (T--) {
       scanf("%d", &amp;n);
       for (int i = 0; i &lt; n; i++) {
           scanf("%d %d", &amp;stick[i].first, &amp;stick[i].second);
        } 
        sort(stick, stick + n);
        memset(dp, -1, sizeof(dp));

        for (int i = 0; i &lt; n; i++) {
            *lower_bound(dp, dp + n, stick[i].second, greater<int> ()) = stick[i].second;  //若某 dp &lt; stick[i].second，将其更新为这个值
        }

        cout <&lt; lower_bound(dp, dp + n, -1, greater&lt;int> ()) - dp &lt;&lt; endl;
    }
    return 0;
}
```

<table id="listStatus" class="table table-striped table-bordered table-responsive dataTable no-footer hover-date" width="100%" cellspacing="0">
<tbody>
<tr id="8388237" class="accepted odd" style="height: 23.675px;">
<td class=" run-id hidden-lg-down" style="height: 23.675px;"><span style="font-size: 10pt;">8388237</span></td>
<td class=" username" style="height: 23.675px;">
<div><span style="font-size: 10pt;">[carolunar](https://vjudge.net/user/carolunar)</span></td>
<td class=" oj" style="height: 23.675px;"><span style="font-size: 10pt;">POJ</span></td>
<td class=" prob_num" style="height: 23.675px;">
<div><span style="font-size: 10pt;">[1065](https://vjudge.net/problem/POJ-1065)</span></td>
<td class=" status hidden-md-down" style="height: 23.675px;">
<div class="view-solution" title="" data-toggle="tooltip" data-run-id="8388237" data-original-title=""><span style="font-size: 10pt;">Accepted</span></td>
<td class=" runtime" style="height: 23.675px;"><span style="font-size: 10pt;">16ms</span></td>
<td class=" memory" style="height: 23.675px;"><span style="font-size: 10pt;">0.2MB</span></td>
<td class=" length hidden-lg-down" style="height: 23.675px;"><span style="font-size: 10pt;">722B</span></td>
<td class=" language" style="height: 23.675px;">
<div class="view-solution self" title="" data-toggle="tooltip" data-html="true" data-run-id="8388237" data-original-title="C++"><span style="font-size: 10pt;">C++</span></td>
<td class=" date" style="height: 23.675px;">
<div class="localizedTime" data-time="1489115780000"><span class="absolute" style="font-size: 10pt;">2017-03-10 11:16:20</span></td>
</tr>
</tbody>
</table>
## 知识水平

今天学习了两个新的用法，lower_bound() 和 greater<int>();
<pre class="">ForwardIterator lower_bound (ForwardIterator first, ForwardIterator last,
                               const T&amp; val, Compare comp);
```

<pre class="">ForwardIterator upper_bound (ForwardIterator first, ForwardIterator last,
                               const T&amp; val, Compare comp);
```

lower_bound 返回 [first, last) 之间第一个使得 Compare 函数为真的指针，如果没有这个参数，默认比较 val &lt;= elem 是否成立。

类似的，upper_bound 返回 [first, last] 之间第一个使得 Compare 函数为真的指针，如果没有这个参数，默认比较 val &lt; elem 是否成立。
```
// lower_bound/upper_bound example
#include <iostream>     // std::cout
#include <algorithm>    // std::lower_bound, std::upper_bound, std::sort
#include <vector>       // std::vector

int main () {
  int myints[] = {10,20,30,30,20,10,10,20};
  std::vector<int> v(myints,myints+8);           // 10 20 30 30 20 10 10 20

  std::sort (v.begin(), v.end());                // 10 10 10 20 20 20 30 30

  std::vector<int>::iterator low,up;
  low=std::lower_bound (v.begin(), v.end(), 20); //          ^
  up= std::upper_bound (v.begin(), v.end(), 20); //                   ^

  std::cout <&lt; "lower_bound at position " &lt;&lt; (low- v.begin()) &lt;&lt; '\n';   //--> 3
  std::cout <&lt; "upper_bound at position " &lt;&lt; (up - v.begin()) &lt;&lt; '\n';   //--> 6

  return 0;
}
```

上述两个函数里面最后一个参数就可以用 greater<int>() 来代替自己写的比较函数。

翻了一下文档，greater 是一个仿函数（function object），类似的还有equal_to, not_equal_to, less, greater_equal 和 less_equal。很好用，具体原理，暂作阙疑。