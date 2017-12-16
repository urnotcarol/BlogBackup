---
title: POJ 3669 Meteor Shower 题解
id: 96
date: 2017-02-12 21:50:54
tags:
- 算法
- OJ
categories:
- 算法
- 搜索
---

<span style="color: #008000;">[POJ 3669 Meteor Shower](http://poj.org/problem?id=3669)</span>

## 题意：

这个同学发现流星雨要来了，会对地球造成伤害于是决定逃跑。N颗流星会不定时降落在坐标轴第一象限300*300内的点上。给出每颗流星降落的坐标和时刻，求这个同学能否成功逃跑，能的话用时多少。

## 思路：

略有一点tricky，无法变通了(@﹏@)~。看了[码农场的代码](http://www.hankcs.com/program/poj-3669-meteor-shower-challenge-programming-contest-2nd-edition-exercises-answers.html)。

这道题没有给出地图，所以需要自己生成地图。

关键的点在于：在生成地图的时候，将每个点的初始值设为无穷大（表示不会有流星），将要被摧毁的点设置为流星降落的时刻。如果原点从一开始就被炸毁，那么直接死亡，否则开始BFS。

从原点开始广度优先地游荡，判断要不要到一个点的依据是该点在地图内，并且该点此前未被访问过，也未被炸毁（可能在将来被炸毁）。每到一个点，当前时间+1.

在游荡过程中一旦发现一个点永远不会被炸（>last变量，或者等于INF？），说明找到了活命点，返回当前所用时间。

如果一直到遍历完毕都找不到这样的点，说明无法逃跑，返回-1。


<!-- more -->
## 代码：

刚开始提交的时候总是Runtime Error，de了半天发现input数组本应该是50000，设置成了5000……这种粗心的错误要少犯啊
```
#include <iostream>
#include <algorithm>
#include <stdio.h>
#include <memory.h>
#include <queue>

using namespace std;

const int MAX_INDEX = 512;
int eMap[MAX_INDEX][MAX_INDEX];
bool visited[MAX_INDEX][MAX_INDEX];

struct Meteor {
    int x;
    int y;
    int t;
};

typedef Meteor P;

Meteor input[50000];
int n;
int last = 0;

int dx[5] = {-1, 0, 1, 0, 0};
int dy[5] = {0, -1, 0, 1, 0};

int bfs() {
    memset(visited, 0, sizeof(visited));
    queue<P> que;
    P cur;
    cur.x = 0;
    cur.y = 0;
    cur.t = 0;
    que.push(cur);

    while (que.size()) {        
        for (int i = 0; i < 4; i++) {
            cur = que.front();
            cur.x += dx[i];
            cur.y += dy[i];
            cur.t++;
            if (cur.x >= 0 && cur.y >= 0 && cur.t < eMap[cur.x][cur.y] && !visited[cur.x][cur.y]) {
                visited[cur.x][cur.y] = true;
                if (eMap[cur.x][cur.y] > last) {
                    return cur.t;
                }                
                que.push(cur);
            }
        }        
        que.pop();
    }
    return -1;
}

int main() {
    cin >> n;
    for (int i = 0; i < n; i++) {
        cin >> input[i].x >> input[i].y >> input[i].t;
    }

    memset(eMap, 0x7f, sizeof(eMap));   //将地图上所有点的值设为极大值（按题意只需大于1000就可） 
    for (int i = 0; i < n; i++) {       //创建地图
        last = max(last, input[i].t);   //last变量记录流星下落的最晚时间 
        for (int j = 0; j < 5; j++) {
            int nx = input[i].x + dx[j];
            int ny = input[i].y + dy[j];
            if (nx >= 0 && ny >= 0 && eMap[nx][ny] > input[i].t) {
                eMap[nx][ny] = input[i].t;
            }
        }     
    }

    if (eMap[0][0] == 0) {
        return -1;
    } 
    else { 
        cout << bfs() << endl;
    }

    return 0;
}
```

这段代码里的结构体变量Meteor，又被typedef为P，不是多此一举。是因为这个变量有两个用途：

1 接收输入流，此时x，y代表流星将要下落的坐标，t代表下落时刻；

2 在BFS的时候，标志该同学此时所处位置，此时x，y代表该同学当前坐标，t代表目前为止所花时间。

Brilliant！

感觉自己并写不出来这样的代码...