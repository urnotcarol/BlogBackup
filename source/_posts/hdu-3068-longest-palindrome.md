---
title: HDU 3068 最长回文 题解 Manacher
id: 286
date: 2017-04-03 21:06:55
tags:
---

## 题意

给定一个字符串，求该字符串中出现的最长的回文串的长度。

## 思路

Manacher 算法的应用题。

Manacher 算法先将给定的字符串进行预处理，在其首尾以及每两个字符的间隔处插入一个字符（如'$', 必须是该字符串中一定不会出现的），如 abc -&gt; $a$b$c$, abcd -&gt; $a$b$c$d$。使得无论是奇数长度还是偶数长度的串，都变成奇数长度，这样避免了分情况处理。

令 p[i] 表示以第 i 个字符为中心的最长的回文串的半径，最终 p[1..n] 中的最大值即为答案。

这个算法很好理解，关键在求 p[i] 的时候巧妙利用了以前得到的信息，降低了复杂度。

令 id 表示当前求出半径的所有回文串里面，向右延伸最远的那个串的中心下标。注意不是半径最长，而是（本身的位置 + 半径）最长。举个例子，一条线段上从左到右每个格点都站了一个人，每个人的胳膊可能不一样长。id 就表示是胳膊向右可以够到最远的那个人的位置，而非胳膊最长的人。

接下来，求点 i 的回文半径的时候，如果 i 处于这个 p[id] + id 之内，i 关于 id 的对称点 id - (i - id) -&gt; 2id - i 也在 id 的左边的半径内。 令 P 表示这个对称点，那么有两种情况：

1.  P 的回文半径小于 p[id]，此时 p[i] 一定等于 p[2id - 1]。
2.  P 的回文半径大于或等于 p[id], 此时令 p[i] 暂且等于这个半径处于 p[id] 内的这一段，再向两边穷举直到不匹配。

<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <string>
#include <string.h>
#include <algorithm>

using namespace std;
const int MAX_N = 110000 + 16;
int p[MAX_N * 2];
char s[MAX_N * 2];

int main() {
    while (scanf("%s", s) != EOF) {  
        int len = strlen(s);      
        for (int i = len; i &gt;= 0; i--) {
            s[i + i + 2] = s[i];
            s[i + i + 1] = '#';
        }
        s[0] = '$';         
        int id = 0;
        int maxLen = 0;
        for (int i = 2; i &lt; 2 * len + 1; i++) {
            if (i &lt; id + p[id]) {
                p[i] = min(p[id] + id - i, p[2 * id - i]);
            }
            else {
                p[i] = 1;
            }
            while (s[i - p[i]] == s[i + p[i]]) {
                p[i]++;
            }
            if (id + p[id] &lt; i + p[i]) {
                id = i;
            }
            if (maxLen &lt; p[i]) {
                maxLen = p[i];
            }
        } 

        cout &lt;&lt; maxLen - 1 &lt;&lt; endl;
    }
    return 0;
}
```

<table id="listStatus" class="table table-striped table-bordered table-responsive dataTable no-footer hover-date" width="100%" cellspacing="0">
<tbody>
<tr id="8644040" class="accepted odd">
<td class=" run-id hidden-lg-down"><span style="font-size: 8pt;">8644040</span></td>
<td class=" username">
<div><span style="font-size: 8pt;">[carolunar](https://vjudge.net/user/carolunar)</span></td>
<td class=" oj"><span style="font-size: 8pt;">HDU</span></td>
<td class=" prob_num">
<div><span style="font-size: 8pt;">[3068](https://vjudge.net/problem/HDU-3068)</span></td>
<td class=" status hidden-md-down">
<div class="view-solution" title="" data-toggle="tooltip" data-run-id="8644040" data-original-title=""><span style="font-size: 8pt;">Accepted</span></td>
<td class=" runtime"><span style="font-size: 8pt;">436</span></td>
<td class=" memory"><span style="font-size: 8pt;">2.8</span></td>
<td class=" length hidden-lg-down"><span style="font-size: 8pt;">1000</span></td>
<td class=" language">
<div class="view-solution shared" title="" data-toggle="tooltip" data-html="true" data-run-id="8644040" data-original-title="C++"><span style="font-size: 8pt;">C++</span></td>
<td class=" date">
<div class="localizedTime" data-time="1491191731000"><span class="absolute" style="font-size: 8pt;">2017-04-03 11:55:31</span></td>
</tr>
</tbody>
</table>