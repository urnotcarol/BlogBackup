---
title: POJ 3009 Curling 2.0 题解
id: 81
date: 2017-02-12 21:30:06
tags:
- 算法
- OJ
categories:
- 算法
- 搜索
---

[POJ3009 Curling 2.0](http://poj.org/problem?id=3009)

思路：DFS + 回溯

// 照着别人的代码写的，测试数据输出正确，但没有AC，没有找到原因

// 经@[Lorazepam](http://www.cnblogs.com/oscar-cnblogs/) 提示，下面这行代码的h和w写反了...

`while(scanf("%d %d", &h, &w) != EOF)`

// 每个地图有n种路线，每条路线都会改变地图，因此每次搜索完毕得到结果（goal）后，要复原地图

// 每次递归DFS，就代表碰到了墙，需改变路线，因此要 step + 1

// 每条路线所用步数不同，题目求最小步数，因此要 goal = min(goal, step)
```
#include <iostream>
#include <stdio.h>

using namespace std;

int w;
int h;
const int INF = 0x3f3f3f3f; //常用的无穷大常量
int goal;
int res[100];
int sx;
int sy;
int dx[4] = {-1, 0, 1, 0};
int dy[4] = {0, 1, 0, -1};

int board[25][25];

bool isNotOut(int x, int y) {
    if(x < 0 || y < 0 || x >= h || y >= w) {
        return false;
    }
    return true;
}

void dfs(int step, int x, int y) {
    int nx;
    int ny;

    if(step > 10) {
        return ;
    }

    for(int i = 0; i < 4; i++) {
        nx = x + dx[i];
        ny = y + dy[i];

        if(!isNotOut(nx, ny)) {
            continue;  
        }
        if(board[nx][ny] == 1) {
            continue;
        }
        while(!board[nx][ny]) {
            nx += dx[i];
            ny += dy[i];
            if(!isNotOut(nx, ny)) {
                break;
            }
        } 
        if(isNotOut(nx, ny)) {
            if(board[nx][ny] == 3) {
                goal = min(goal, step);
            }
            if(board[nx][ny] == 1) {
                board[nx][ny] = 0;
                dfs(step + 1, nx - dx[i], ny - dy[i]);
                board[nx][ny] = 1;
            }
        }
    }
}

int main() {
    int n = 0;
    while(scanf("%d %d", &h, &w) != EOF) {
        if(w == 0 && h == 0) {
            break;
        }
        for(int i = 0; i < h; i++) {
            for(int j = 0; j < w; j++) {
                scanf("%d", &board[i][j]);
                if(board[i][j] == 2) {
                    sx = i;
                    sy = j;
                    board[i][j] = 0;
                }
            }
        }
        goal = INF;
        dfs(1, sx, sy);   
        res[n] = goal;
        n++;    
    }
    for (int i = 0; i < n; i++) {
        if(res[i] != INF) {
            printf("%d\n", res[i]);
        } else {
            printf("-1\n");
        }
    }
    return 0;
}
```

额外收获：

while (scanf() != EOF) 或者 while (~scanf())，结合 if(w == 0 && h == 0) break; 用来判断测试数据输入完毕