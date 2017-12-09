---
title: a
id: 355
tags:
---

<pre class="lang:default decode:true ">#include &lt;iostream&gt;
#include &lt;stdio.h&gt;

using namespace std;

const int MAX_M = 1000;
int n;
int k;
int m;
int price[MAX_M];

int solve() {
    int res = 0;    
    int comb = 1 &lt;&lt; m;      //m个菜中任意选菜的方案数 
    for (int i = 0; i &lt; comb; i++) {
        int ordered = 0;    //每种方案中被选中的菜的个数 
        int priceSum = 0;   
        for (int j = 0; j &lt; m; j++) {
            if (i &amp; (1 &lt;&lt; j)) {
                ordered++;
                priceSum += price[j];
            }
        }
        if (ordered == k &amp;&amp; priceSum &lt;= n) {
            res++;
        }
    }
    return res;
}

int main() {    
    scanf("%d %d %d", &amp;n, &amp;k, &amp;m);
    for (int i = 0; i &lt; m; i++) {
        scanf("%d", &amp;price[i]);
    }
    printf("%d", solve());
    return 0;
}</pre>
&nbsp;