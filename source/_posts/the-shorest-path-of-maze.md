---
title: 迷宫最短路径问题 题解
id: 87
date: 2017-02-12 21:36:39
tags:
---

《挑战程序设计竞赛》2.1.5 例题

问题：给定一个大小为N×M的迷宫。迷宫由通道和墙壁组成，每一步可以向邻接的上下左右四格的通道移动。请求出从起点到终点所需的最小步数。请注意，本题假定从起点一定可以移动到终点。

限制条件：N, M ≦100

示例输入：

10 10
#S######.#
......#..#
.#.##.##.#
.#........
##.##.####
....#....#
.#######.#
....#.....
.####.###.
....#...G#

示例输出：

22
```
#include <stdio.h>
#include <iostream>
#include <queue>

using namespace std;
int m;
int n;
const int INF = 0x3f3f3f3f;
char maze[100][100];
int d[100][100];
int sx, sy;
int gx, gy;
int dx[4] = { -1, 0, 1, 0 };
int dy[4] = { 0, 1, 0, -1 };
typedef pair<int, int> P;

void bfs() {
    queue<P> q;
    q.push(P(sx, sy));
    d[sx][sy] = 0;

    while (q.size()) {
        int x = q.front().first;
        int y = q.front().second;
        q.pop();
        if (x == gx &amp;&amp; y == gy) {
            break;
        }
        for (int i = 0; i &lt; 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            if (nx &gt;= 0 &amp;&amp; nx < n &amp;&amp; ny >= 0 &amp;&amp; ny &lt; m &amp;&amp; maze[nx][ny] != '#' &amp;&amp; d[nx][ny] == INF) {
                q.push(P(nx, ny));
                d[nx][ny] = d[x][y] + 1;
            }
        }

    }

}

int main() {
    cin &gt;&gt; m &gt;&gt; n;
    for (int i = 0; i &lt; n; i++) {
        for (int j = 0; j &lt; m; j++) {
            cin &gt;&gt; maze[i][j];
            d[i][j] = INF;
            if (maze[i][j] == 'S') {
                sx = i;
                sy = j;
            }
            if (maze[i][j] == 'G') {
                gx = i;
                gy = j;
            }
        }
    }
    bfs();
    cout &lt;&lt; d[gx][gy];
    return 0;
}
```

DFS一般直接利用系统栈（通过在代码中使用递归），而BFS需要自己维护一个队列