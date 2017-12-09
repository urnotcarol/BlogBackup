---
title: AOJ 0033 Ball 题解
id: 104
date: 2017-02-12 22:02:15
tags:
---

<span style="color: #008000;">[AOJ 0033 Ball ](https://vjudge.net/problem/22516/origin)</span>

思路：二进制枚举，用了昨天学到的2^N以及与运算方法枚举。

<!-- more -->

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int n;
int ball[10]; 
vector<int> l;
vector<int> r;

bool solve() {
    bool res = false;
    for (int i = 0; i &lt; 1024; i++) {
        for (int j = 0; j &lt; 10; j++) {
            if (i &amp; (1 &lt;&lt; j)) {
                l.push_back(ball[j]);
            }
            else {
                r.push_back(ball[j]);
            }
        }
        int lLen = l.size();
        int rLen = r.size();
        bool lOK = true;
        bool rOK = true;        
        for (int j = 0; j &lt; lLen - 1; j++) {
            if (l.at(j) &gt; l.at(j + 1)) {
                lOK = false;
                break;
            }
        }
        for (int j = 0; j &lt; rLen - 1; j++) {
            if (r.at(j) &gt; r.at(j + 1)) {
                rOK = false;
                break;
            }
        }
        if (lOK &amp;&amp; rOK) {
            res = true;
            break;
        }
        l.clear();
        r.clear();        
    }
    return res;
}

int main() {
    cin &gt;&gt; n;
    for (int i = 0; i &lt; n; i++) {
        for (int j = 0; j &lt; 10; j++) {
            cin &gt;&gt; ball[j];
        }        
        if (solve()) {
            cout &lt;&lt; "YES" &lt;&lt; endl;
        } 
        else {
            cout &lt;&lt; "NO" &lt;&lt; endl;
        }
    }
    return 0;
}
```

&nbsp;

