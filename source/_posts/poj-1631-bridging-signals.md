---
title: POJ 1631 Bridging signals 题解
id: 163
date: 2017-03-10 15:39:49
tags:
---


[POJ 1631 Bridging signals](http://poj.org/problem?id=1631)


## 题意

铺垫太长以至于看了好久才懂……有人把电路板上的电线弄乱了，如左图。只有相互不交叉的电线才是可用的。给出和左边 1-p 连线的右边的端口顺序，求最多有多少条电线可用。

![](https://odzkskevi.qnssl.com/226e776304cadfd23e680d3bc5e28f06?v=1488518603)

## 思路

虽然铺垫了这么多，但本质上就是求最长上升子序列的长度的问题。只要右边的顺序上升的话，电线就不会缠在一起。

一开始并没有想到，做完前一道题加提醒加背代码……很快写完了。

还是要多学习一个，这样才能跟不同的人谈笑风生，啊不，见到不同的题才能抓住本质。

## AC代码
```
#include <iostream>
#include <stdio.h>
#include <algorithm>

using namespace std;

const int MAX_P = 40000;
const int INF = 0x3f3f3f3f;
int n;
int p;
int port[MAX_P + 4];
int dp[MAX_P + 4];

int main() {
    scanf("%d", &amp;n);
    while (n--) {
        scanf("%d", &amp;p);
        for (int i = 0; i &lt; p; i++) {
            scanf("%d", &amp;port[i]);
            dp[i] = INF;
        }

        for (int i = 0; i &lt; p; i++) {
            *lower_bound(dp, dp + p, port[i]) = port[i];
        }

        cout &lt;&lt; lower_bound(dp, dp + p, INF) - dp &lt;&lt; endl;
    }
    return 0;
}

```

<table id="listStatus" class="table table-striped table-bordered table-responsive dataTable no-footer hover-date" width="100%" cellspacing="0">
<tbody>
<tr id="8389690" class="accepted odd">
<td class=" run-id hidden-lg-down"><span style="font-size: 10pt;">8389690</span></td>
<td class=" username">
<div><span style="font-size: 10pt;">[carolunar](https://vjudge.net/user/carolunar)</span></td>
<td class=" oj"><span style="font-size: 10pt;">POJ</span></td>
<td class=" prob_num">
<div><span style="font-size: 10pt;">[1631](https://vjudge.net/problem/POJ-1631)</span></td>
<td class=" status hidden-md-down">
<div class="view-solution" title="" data-toggle="tooltip" data-run-id="8389690" data-original-title=""><span style="font-size: 10pt;">Accepted</span></td>
<td class=" runtime"><span style="font-size: 10pt;">125ms</span></td>
<td class=" memory"><span style="font-size: 10pt;">0.5MB</span></td>
<td class=" length hidden-lg-down"><span style="font-size: 10pt;">580B</span></td>
<td class=" language">
<div class="view-solution self" title="" data-toggle="tooltip" data-html="true" data-run-id="8389690" data-original-title="C++"><span style="font-size: 10pt;">C++</span></td>
<td class=" date">
<div class="localizedTime" data-time="1489131005000"><span class="absolute" style="font-size: 10pt;">2017-03-10 15:30:05</span></td>
</tr>
</tbody>
</table>