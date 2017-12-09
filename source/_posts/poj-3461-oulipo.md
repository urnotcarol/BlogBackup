---
title: POJ 3461 Oulipo 题解 KMP
id: 289
date: 2017-04-03 21:23:16
tags:
---

## 题意

给定两个字符串 W 和 T，求 W 在 T 中出现的次数。

## 思路

KMP 算法的应用题。

看了一天可算是看明白了。

感想：感性层面理解一个算法和落实到代码上，还差了 10 页《算法导论》吧。毕竟传说中，数据结构老师上小学的女儿都可以帮她批阅期末考试的卷子...

引用一篇别人的博客（[阮一峰](http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html)），便于温习感性认识。至于实现细节，需要看某导论和代码。
> <span style="font-size: 12pt;">　　[字符串匹配](http://en.wikipedia.org/wiki/String_searching_algorithm)是计算机的基本任务之一。</span>
> 
> 
> <span style="font-size: 12pt;">举例来说，有一个字符串"BBC ABCDAB ABCDABCDABDE"，我想知道，里面是否包含另一个字符串"ABCDABD"？</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140953-cdb5bb8b593240b3b78958b45c07fadd.jpg)</span>
> 
> 
> <span style="font-size: 12pt;">许多算法可以完成这个任务，[Knuth-Morris-Pratt算法](http://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm)（简称KMP）是最常用的之一。它以三个发明者命名，起头的那个K就是著名科学家Donald Knuth。</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140953-fb77ecc66f8f416daa38395d5cf61fa6.jpg)</span>
> 
> 
> <span style="font-size: 12pt;">这种算法不太容易理解，网上有很多[解释](http://www.google.com/search?q=Knuth-Morris-Pratt+algorithm)，但读起来都很费劲。直到读到[Jake Boxer](http://jakeboxer.com/blog/2009/12/13/the-knuth-morris-pratt-algorithm-in-my-own-words/)的文章，我才真正理解这种算法。下面，我用自己的语言，试图写一篇比较好懂的KMP算法解释。</span>
> 
> 
> <span style="font-size: 12pt;">1.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140954-a6364372f7cb42219c25c9a42ca91f2e.png)</span>
> 
> 
> <span style="font-size: 12pt;">首先，字符串"BBC ABCDAB ABCDABCDABDE"的第一个字符与搜索词"ABCDABD"的第一个字符，进行比较。因为B与A不匹配，所以搜索词后移一位。</span>
> 
> 
> <span style="font-size: 12pt;">2.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140954-4d282a1fa8e644f69c0ad9a96b8d4e0f.png)</span>
> 
> 
> <span style="font-size: 12pt;">因为B与A不匹配，搜索词再往后移。</span>
> 
> 
> <span style="font-size: 12pt;">3.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140954-403557bcb10e41dbbce9434da36d105c.png)</span>
> 
> 
> <span style="font-size: 12pt;">就这样，直到字符串有一个字符，与搜索词的第一个字符相同为止。</span>
> 
> 
> <span style="font-size: 12pt;">4.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140954-c084752484ac48eaa930a765095ed139.png)</span>
> 
> 
> <span style="font-size: 12pt;">接着比较字符串和搜索词的下一个字符，还是相同。</span>
> 
> 
> <span style="font-size: 12pt;">5.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140955-99c6a1d36b9e418c96de092b8ca1f5ed.png)</span>
> 
> 
> <span style="font-size: 12pt;">直到字符串有一个字符，与搜索词对应的字符不相同为止。</span>
> 
> 
> <span style="font-size: 12pt;">6.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140955-90c93e1401c843148af09b472400e613.png)</span>
> 
> 
> <span style="font-size: 12pt;">这时，最自然的反应是，将搜索词整个后移一位，再从头逐个比较。这样做虽然可行，但是效率很差，因为你要把"搜索位置"移到已经比较过的位置，重比一遍。</span>
> 
> 
> <span style="font-size: 12pt;">7.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140955-4da97276b7aa4ab0bc760623d8f59434.png)</span>
> 
> 
> <span style="font-size: 12pt;">一个基本事实是，当空格与D不匹配时，你其实知道前面六个字符是"ABCDAB"。KMP算法的想法是，设法利用这个已知信息，不要把"搜索位置"移回已经比较过的位置，继续把它向后移，这样就提高了效率。</span>
> 
> 
> <span style="font-size: 12pt;">8.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140956-16bec39296b74bcaaf0627e20b846120.png)</span>
> 
> 
> <span style="font-size: 12pt;">怎么做到这一点呢？可以针对搜索词，算出一张《部分匹配表》（Partial Match Table）。这张表是如何产生的，后面再介绍，这里只要会用就可以了。</span>
> 
> 
> <span style="font-size: 12pt;">9.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140955-14131127b6574a3391b337024614306a.png)</span>
> 
> 
> <span style="font-size: 12pt;">已知空格与D不匹配时，前面六个字符"ABCDAB"是匹配的。查表可知，最后一个匹配字符B对应的"部分匹配值"为2，因此按照下面的公式算出向后移动的位数：</span>
> 
> > <span style="font-size: 12pt;">　　移动位数 = 已匹配的字符数 - 对应的部分匹配值</span>
> 
> <span style="font-size: 12pt;">因为 6 - 2 等于4，所以将搜索词向后移动4位。</span>
> 
> 
> <span style="font-size: 12pt;">10.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140955-9707cee873fd452291d0638b295a3b9d.png)</span>
> 
> 
> <span style="font-size: 12pt;">因为空格与Ｃ不匹配，搜索词还要继续往后移。这时，已匹配的字符数为2（"AB"），对应的"部分匹配值"为0。所以，移动位数 = 2 - 0，结果为 2，于是将搜索词向后移2位。</span>
> 
> 
> <span style="font-size: 12pt;">11.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140959-bf69aab69d114c1da30362d3aeb57bdd.png)</span>
> 
> 
> <span style="font-size: 12pt;">因为空格与A不匹配，继续后移一位。</span>
> 
> 
> <span style="font-size: 12pt;">12.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140956-7b7bf61a842f46a8949cc1d084a56f79.png)</span>
> 
> 
> <span style="font-size: 12pt;">逐位比较，直到发现C与D不匹配。于是，移动位数 = 6 - 2，继续将搜索词向后移动4位。</span>
> 
> 
> <span style="font-size: 12pt;">13.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140957-b6c5a85c046244bf8805b9c0fb88e829.png)</span>
> 
> 
> <span style="font-size: 12pt;">逐位比较，直到搜索词的最后一位，发现完全匹配，于是搜索完成。如果还要继续搜索（即找出全部匹配），移动位数 = 7 - 0，再将搜索词向后移动7位，这里就不再重复了。</span>
> 
> 
> <span style="font-size: 12pt;">14.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140956-8bf1c8f8454a4e269e287a2f7afe1a45.png)</span>
> 
> 
> <span style="font-size: 12pt;">下面介绍《部分匹配表》是如何产生的。</span>
> 
> 
> <span style="font-size: 12pt;">首先，要了解两个概念："前缀"和"后缀"。 "前缀"指除了最后一个字符以外，一个字符串的全部头部组合；"后缀"指除了第一个字符以外，一个字符串的全部尾部组合。</span>
> 
> 
> <span style="font-size: 12pt;">15.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140958-0ff0d174ed79413d9052797fab044596.png)</span>
> 
> 
> <span style="font-size: 12pt;">"部分匹配值"就是"前缀"和"后缀"的最长的共有元素的长度。以"ABCDABD"为例，</span>
> 
> > <span style="font-size: 12pt;">　　－　"A"的前缀和后缀都为空集，共有元素的长度为0；</span>
> > 
> > 
> > <span style="font-size: 12pt;">－　"AB"的前缀为[A]，后缀为[B]，共有元素的长度为0；</span>
> > 
> > 
> > <span style="font-size: 12pt;">－　"ABC"的前缀为[A, AB]，后缀为[BC, C]，共有元素的长度0；</span>
> > 
> > 
> > <span style="font-size: 12pt;">－　"ABCD"的前缀为[A, AB, ABC]，后缀为[BCD, CD, D]，共有元素的长度为0；</span>
> > 
> > 
> > <span style="font-size: 12pt;">－　"ABCDA"的前缀为[A, AB, ABC, ABCD]，后缀为[BCDA, CDA, DA, A]，共有元素为"A"，长度为1；</span>
> > 
> > 
> > <span style="font-size: 12pt;">－　"ABCDAB"的前缀为[A, AB, ABC, ABCD, ABCDA]，后缀为[BCDAB, CDAB, DAB, AB, B]，共有元素为"AB"，长度为2；</span>
> > 
> > 
> > <span style="font-size: 12pt;">－　"ABCDABD"的前缀为[A, AB, ABC, ABCD, ABCDA, ABCDAB]，后缀为[BCDABD, CDABD, DABD, ABD, BD, D]，共有元素的长度为0。</span>
> 
> <span style="font-size: 12pt;">16.</span>
> 
> 
> <span style="font-size: 12pt;">![](http://images.cnitblog.com/news/331782/201305/02140958-eb88ca7ad6d54fe4b44dc9ec0061f745.png)</span>
> 
> 
> <span style="font-size: 12pt;">"部分匹配"的实质是，有时候，字符串头部和尾部会有重复。比如，"ABCDAB"之中有两个"AB"，那么它的"部分匹配值"就是2（"AB"的长度）。搜索词移动的时候，第一个"AB"向后移动4位（字符串长度-部分匹配值），就可以来到第二个"AB"的位置。</span>

* * *

*** 另外**，《算法导论》 32.4节，590页有一处错误，第一段示例代码 12 行的 q = Pi(q) 需要再向右缩进一层。

![](http://www.carolunar.com/wp-content/uploads/2017/04/QQ图片20170403212005.png)

*** 再另外，**我的体会是，不要去想那么多“模式串右移几位”的事，直接看做是比较两个串的第几位和第几位，更直观和容易理解一点，又不是非得对齐了才能比较。我觉得斜着瞅一眼要比想着怎么移动省事一些。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <string.h>
#include <memory.h>

using namespace std;

const int MAX_N = 1000000 + 16;

char W[MAX_N];
char T[MAX_N];
int Pi[MAX_N];

void computePrefix(char * P) {
    memset(Pi, 0, sizeof(Pi));
    int n = strlen(P);
    int k = 0;
    Pi[0] = 0;
    for (int q = 1; q &lt; n; q++) {
        while (k &gt; 0 &amp;&amp; P[q] != P[k]) {
            k = Pi[k - 1];
        }
        if (P[q] == P[k]) {
            k++;
        }
        Pi[q] = k;
    }
}

int KMP(char * T, char * P) {
    int res = 0;
    int n = strlen(T);
    int m = strlen(P);
    int q = 0;       //numbers of characters matched
    computePrefix(P);
    for (int i = 0; i &lt; n; i++) {
        while (q &gt; 0 &amp;&amp; P[q] != T[i]) {
            q = Pi[q - 1];
        }
        if (P[q] == T[i]) {
            q++;
        }
        if (q == m) {
            res++;
            q = Pi[q - 1];
        }
    }
    return res;
}

int main() {
    int C;
    scanf("%d", &amp;C);
    while (C--) {
        scanf("%s", W);
        scanf("%s", T);
        printf("%d\n", KMP(T, W));
    }
    return 0;
}
```

<table id="listStatus" class="table table-striped table-bordered table-responsive dataTable no-footer hover-date" width="100%" cellspacing="0">
<tbody>
<tr id="8650221" class="accepted odd">
<td class=" run-id hidden-lg-down"><span style="font-size: 10pt;">8650221</span></td>
<td class=" username">
<div><span style="font-size: 10pt;">[carolunar](https://vjudge.net/user/carolunar)</span></td>
<td class=" oj"><span style="font-size: 10pt;">POJ</span></td>
<td class=" prob_num">
<div><span style="font-size: 10pt;">[3461](https://vjudge.net/problem/POJ-3461)</span></td>
<td class=" status hidden-md-down">
<div class="view-solution" title="" data-toggle="tooltip" data-run-id="8650221" data-original-title=""><span style="font-size: 10pt;">Accepted</span></td>
<td class=" runtime"><span style="font-size: 10pt;">157</span></td>
<td class=" memory"><span style="font-size: 10pt;">5.1</span></td>
<td class=" length hidden-lg-down"><span style="font-size: 10pt;">1182</span></td>
<td class=" language">
<div class="view-solution shared" title="" data-toggle="tooltip" data-html="true" data-run-id="8650221" data-original-title="C++"><span style="font-size: 10pt;">C++</span></td>
<td class=" date">
<div class="localizedTime" data-time="1491221690000"><span class="absolute" style="font-size: 10pt;">2017-04-03 20:14:50</span></td>
</tr>
</tbody>
</table>