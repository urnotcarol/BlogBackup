---
title: AOJ 0558 Cheese 题解
id: 92
date: 2017-02-12 21:44:32
tags:
---

<span style="color: #008000;">[AOJ 0558 Cheese](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=0558)</span>

## 题意

在H * W的地图上有N个奶酪工厂，分别生产硬度为1-N的奶酪。有一只吃货老鼠准备从老鼠洞出发吃遍每一个工厂的奶酪。老鼠有一个体力值，初始时为1，每吃一个工厂的奶酪体力值增加1（每个工厂只能吃一次），且老鼠只能吃硬度不大于当前体力值的奶酪。
老鼠从当前格走到相邻的无障碍物的格（上下左右）需要时间1单位，有障碍物的格不能走。走到工厂上时即可吃到该工厂的奶酪，吃奶酪时间不计。问吃遍所有奶酪最少用时。
输入：第一行三个整数H(1 &lt;= H &lt;= 1000)、W(1 &lt;= W &lt;=1000)、N(1 &lt;= N &lt;= 9)，之后H行W列为地图， “.“为空地， ”X“为障碍物，”S“为老鼠洞， 1-N代表硬度为1-N的奶酪的工厂。

入力例 1
3 3 1
S..
...
..1

出力例 1
4

入力例 2
4 5 2
.X..1
....X
.XX.S
.2.X.

出力例 2
12

入力例 3
10 10 9
.X...X.S.X
6..5X..X1X
...XXXX..X
X..9X...X.
8.X2X..X3X
...XX.X4..
XX....7X..
X..X..XX..
X...X.XX..
..X.......

出力例 3
91

## 思路

老鼠初始体力值为1，并且只能吃硬度不大于当前体力值的奶酪，每吃一个奶酪体力值增加1。有N个奶酪，硬度分别为1~N。说明老鼠只能按照1~N的顺序吃。比如有5个奶酪，硬度分别是1、2、3、4、5，老鼠只能按照这个顺序吃，吃完之后的体力值分别为2、3、4、5、6。

看起来复杂的条件实际上降低了搜索难度，只需将递增的数字当做终点逐个搜索，相当于正常的BFS循环N次。


<!-- more -->
## 代码
```
#include <iostream>
#include <stdio.h>
#include <deque>

using namespace std;

const int INF = 0x3f3f3f3f;
typedef pair<int, int> P;
int h, w, n;
int sx, sy;
int gx, gy;
char cmap[1000][1000];
int goalSum;
int goal[10];
int d[1000][1000];
int dx[4] = {-1, 0, 1, 0};
int dy[4] = {0, -1, 0, 1};

int bfs() {
    int res = 0;
    deque<P> que;
    for (int ci = 0; ci &lt; n; ci++) {  //要进行n次循环，寻找n次终点，每次步数计入goal[i], 最后累加起来是res 
        for (int i = 0; i &lt; h; i++) {
            for (int j = 0; j &lt; w; j++) {
                d[i][j] = INF;
            }
        }

        que.push_back(P(sx, sy));
        d[sx][sy] = 0;

        while(que.size()) {            
            int x = que.front().first;
            int y = que.front().second;

            que.pop_front();

            if ((int)cmap[x][y] == 49 + ci) {
                goal[ci] = d[x][y];
                sx = x;
                sy = y;
                break;
            }

            for (int i = 0; i &lt; 4; i++) {
                int nx = x + dx[i];
                int ny = y + dy[i];
                if (nx &gt;=0 &amp;&amp; nx < h &amp;&amp; ny >= 0 &amp;&amp; ny &lt; w &amp;&amp; cmap[nx][ny] != 'X' &amp;&amp; d[nx][ny] == INF) {
                    que.push_back(P(nx, ny));
                    d[nx][ny] = d[x][y] + 1;
                }
            }            
        }
        que.clear(); 
        res += goal[ci];
    }
    return res;
}

int main() {
    cin &gt;&gt; h &gt;&gt; w &gt;&gt; n;
    for (int i = 0; i &lt; h; i++) {
        for (int j = 0; j &lt; w; j++) {
            cin &gt;&gt; cmap[i][j];
            if (cmap[i][j] == 'S') {
                sx = i;
                sy = j;
            }
        }
    }
    goalSum = bfs();
    cout &lt;&lt; goalSum &lt;&lt; endl; //不换行会PE
    return 0;
}
```


*   通常 BFS 使用队列 queue 即可，这里由于多次循环 BFS，每次要清空队列，故用了 STL 的双端队列 deque，clear() 函数用来清空。
*   deque 常用函数有：front(), back(), push_front(), push_back(), pop_front(), pop_back()。头尾均可以访问、添加、删除。
*   d[x][y]在记录起点到某坐标的步数的同时，还起到标志该点是否被搜索过的作用。
*   queue的empty()函数不是用来清空的！是返回该队列是否为空&gt; &lt; (debug了很久_(:зゝ∠)_)