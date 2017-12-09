---
title: POJ 3050 Hopscotch 题解
id: 100
date: 2017-02-12 21:55:22
tags:
---

[POJ 3050 Hopscotch](http://poj.org/problem?id=3050)

思路：

并无太好的...看了@[Lorazepam](http://www.cnblogs.com/oscar-cnblogs/p/6298493.html)的代码

原本的思路是用 vector<int> 存放生成的整数，这样还需要在放进去之前把位数乘基数变成相应整数，而且需要对 vector 去重

然后才发现有 set 这一好用容器，插入值重复的话会插入失败，因此最后只要用 .size() 就可以得到所有的可能性了。并且不用bother to生成整数，直接当成字符串放进去就好了。

用DFS，网格上的每一点都依次作为起点开始深搜，每跳一步 n 就加 1，当 n 为 6 时，说明前面已经有 0~5 这 6 步了， 返回。

因为有 n 为 6 做限制，所以就算可以重复访问，也不会无限循环。

&nbsp;

还需进一步领会深搜和广搜的精神。
```
#include <iostream>
#include <set>
#include <string>

using namespace std;

int grid[5][5];
int dx[4] = {-1, 0, 1, 0};
int dy[4] = {0, -1, 0, 1};
char digits[6];
set<string> integers;

void dfs(int x, int y, int n) {
    if (n == 6) {
        string temp = digits;
        integers.insert(temp);
        return;
    }
    digits[n] = grid[x][y] + '0';
    for (int i = 0; i &lt; 4; i++) {
        int nx = x + dx[i];
        int ny = y + dy[i];
        if (nx &gt;= 0 &amp;&amp; nx < 5 &amp;&amp; ny >= 0 &amp;&amp; ny &lt; 5) {
            dfs(nx, ny, n + 1);
        }
    }      
}

int main() {
    for (int i = 0; i &lt; 5; i++) {
        for (int j = 0; j &lt; 5; j++) {
            cin &gt;&gt; grid[i][j];
        }
    }

    for (int i = 0; i &lt; 5; i++) {
        for (int j = 0; j &lt; 5; j++) {
            dfs(i, j, 0);
        }
    }   
    cout &lt;&lt; integers.size();
    return 0; 
}
```

&nbsp;