---
title: AOJ 0121 Seven Puzzle 题解 BFS + 康托展开
id: 343
date: 2017-04-17 16:21:34
categories:
- 算法
tags: BFS OJ
---

[AOJ 0121 Seven Puzzle](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=0121)

### 题意

4 * 2 的方格，填充了 0 - 7 这八个数字。相邻格子可以交换内容。给定任意一组序列，求最少多少次交换可以使其变成顺序排列。

![](http://judge.u-aizu.ac.jp/onlinejudge/IMAGE1/sevenpuzzle1.gif)

### 思路

BFS + 康托展开判重。是迷宫类问题的升级版，升级之处在于是一个动态的地图，每次搜索都会改变它，因此不能用单一变量来表示地图。

所以每次入队的不是移动对象当前走到了哪里，而是一整张地图的状态。

可以用康托展开+数组判重，也可以用别的 hash 函数配合 STL 的 map使用，后者就不要求映射后在尽量小的范围内了。

另外一种判重的方法，是根本不展开，把一整个地图当做字符串处理，这样由于字符串本身的连续性以及相互之间的相异性，可以配合 map 轻松地判重~


<!-- more -->
### 代码
```
#include <iostream>
#include <stdio.h>
#include <queue>

using namespace std;

const int MAX_S = 40320 + 16;
const int INF = 0x3f3f3f3f;
int fact[10] = {1, 1, 2, 6, 24, 120, 720, 5040, 40320};
int a[16];
int n = 7;
int dp[MAX_S];

struct State {
    int a[16];
    int pos;
    int contor() { //康托展开，返回特定序列所对应的整数
        int res = 0;
        for (int i = 0; i <= n; i++) {
            int count = 0;
            for (int j = i + 1; j <= n; j++) {
                if (a[j] < a[i]) {
                    count++;
                }
            }
            res += count * fact[n - i];
        }
        return res;
    }
};
int dx[4] = {1, -1, 4, -4};

void bfs() {
    State ini;
    for (int i = 0; i <= n; i++) {
        ini.a[i] = i;
    }
    ini.pos = 0;
    queue<State> que;
    que.push(ini);
    dp[0] = 0;
    while(!que.empty()) {
        State cur = que.front();
        que.pop();
        for (int i = 0; i < 4; i++) {
            int p = cur.pos + dx[i];
            if (p >= 0 &amp;&amp; p <= 7
                &amp;&amp; !(cur.pos == 3 &amp;&amp; p == 4)
                &amp;&amp; !(cur.pos == 4 &amp;&amp; p == 3)) {
                State next = cur;
                swap(next.a[p], next.a[cur.pos]);
                next.pos = p;
                int contorH = next.contor();
                if (dp[contorH] == INF) {
                    dp[contorH] = dp[cur.contor()] + 1;
                    que.push(next);
                }
            }
        }
    }
}

int main() {
    fill(dp, dp + MAX_S, INF);
    string line;
    bfs();
    while(getline(cin, line)) {
        State cur;
        for (int i = 0; i <= n; i++) {
            cur.a[i] = line[2 * i] - '0';
        }
        printf("%d\n", dp[cur.contor()]);
    }
}
```

PS，第八届蓝桥杯省赛第二道填空题跳蚂蚱和这道题很像。
```
#include <iostream>
#include <stdio.h>
#include <queue>
#include <memory.h>

using namespace std;

const int MAX_S = 362880 + 16;
int fact[10] = {1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880};
int dx[4] = {-2, -1, 1, 2};
bool visited[MAX_S];
int n = 8;

struct State {
    int a[21];
    int pos;
    int step;
    bool isAns() {
        bool res = true;
        for (int i = 1; i <= n; i++) {
            if (a[i] != (n + 1 - i)) {
                res = false;
                break;
            }
        }
        return res;
    }
    int contor() {
        int res = 0;
        for (int i = 0; i <= n; i++) {
            int count = 0;
            for (int j = i + 1; j <= n; j++) {
                if (a[j] < a[i]) {
                    count++;
                }
            }
            res += fact[n - i] * count;
        }
        return res;
    }
};

void bfs() {
    fill(visited, visited + MAX_S, false);
    State ini;
    for (int i = 0; i <= n; i++) {
        ini.a[i] = i;
    }
    ini.pos = 0;
    ini.step = 0;

    queue<State> que;
    que.push(ini);

    while(!que.empty()) {
        State cur = que.front();
        que.pop();
        int contorH = cur.contor();
        if (visited[contorH]) {
            continue;
        }
        visited[contorH] = true;
        if (cur.isAns()) {
            printf("%d", cur.step);
            break;
        }
        for (int i = 0; i < 4; i++) {
            State next = cur;
            int x = next.pos;
            int y = (next.pos + dx[i] + n + 1) % (n + 1);
            swap(next.a[x], next.a[y]);
            next.pos = y;
            next.step++;
            que.push(next);
        }
    }
}

int main() {
    bfs();
    return 0;
}
```
### 参考

[码农场](http://www.hankcs.com/) » [AOJ 0121: Seven Puzzle 《挑战程序设计竞赛(第2版)》练习题答案](http://www.hankcs.com/program/aoj-0121-seven-puzzle-challenge-programming-contest-2nd-edition-exercises-answers.html)

[大圣给的题解](http://paste.ubuntu.com/24397737/)