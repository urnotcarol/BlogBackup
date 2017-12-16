---
title: POJ 1979 Red and Black 题解
id: 76
date: 2017-02-12 21:05:44
tags:
- 算法
- OJ
categories:
- 算法
- 搜索
---

[POJ 1979 Red and Black](http://poj.org/problem?id=1979)

## 题意

矩形屋顶上被覆盖了 H * W 的红色和黑色的瓦片。小明一开始站在黑瓦上，他可以向上下左右四个方向的黑瓦移动，但红瓦不行。给出小明的初始位置，问他可以移动到的位置总共有多少个。

## 思路

深度优先搜索
```
`#include <iostream>

using namespace std;

int w[100000000];
int h[100000000];
int res[100000000];
char tiles[20][20];

void dfs(int index, int x, int y) {
    tiles[x][y] = '#';
    int dx[4] = {-1, 0, 1, 0};
    int dy[4] = {0, -1, 0, 1};
    for (int i = 0; i < 4; i++) {
        int nx = x + dx[i];
        int ny = y + dy[i];
        if (nx >= 0 && ny >= 0 && nx < h[index] && ny < w[index] && tiles[nx][ny] == '.') {
            res[index]++;
            dfs(index, nx, ny);
        }
    } 
    return;
}

void solve(int index) {
    res[index] = 1;
    for (int i = 0; i < h[index]; i++) {
        for (int j = 0; j < w[index]; j++) {
            if (tiles[i][j] == '@') {
                dfs(index, i, j);
            }
        }
    }
} 
int main() {
    int index = 0;
    do {
        cin >> w[index] >> h[index];
         for (int i = 0; i < h[index]; i++) {
            for (int j = 0; j < w[index]; j++) {
               cin >> tiles[i][j];
           }
        }
        solve(index);
        index++;
    } 
    while (w[index - 1] > 0 && h[index - 1] > 0);

    for (int i = 0; i < index -1; i++) {
        cout << res[i] << endl;
    }

    return 0;
}
```

//第一个AC的OJ题目，加油

## 2017.4.7 温习

重写了一下
```
#include <iostream>
#include <stdio.h>
#define ONLINE_JUDGE
using namespace std;
const int MAX_N = 20 + 4;
int H;
int W;
char tile[MAX_N][MAX_N];
int sx;
int sy;
int res;
int dx[4] = {-1, 0, 1, 0};
int dy[4] = {0, -1, 0, 1};

void dfs(int x, int y) {
    int nx;
    int ny;
    tile[x][y] = '#';
    for (int i = 0; i < 4; i++) {
        nx = x + dx[i];
        ny = y + dy[i];
        if (nx >= 0 && nx < H && ny >= 0 && ny < W && tile[nx][ny] == '.') {
            res++;
//            tile[nx][ny] = '#';
            dfs(nx, ny);
        }
    }
    return;
}

int main() {
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    #endif
    while (scanf("%d %d", &W, &H) && W != 0) {
        for (int i = 0; i < H; i++) {
            cin.ignore();
            for (int j = 0; j < W; j++) {
                scanf("%c", &tile[i][j]);
                if (tile[i][j] == '@') {
                    sx = i;
                    sy = j;
                    tile[i][j] = '.';
                }
            }
        }
        res = 1;
        dfs(sx, sy);
        printf("%d\n", res);        
    }
    #ifndef ONLINE_JUDGE
    fclose(stdin);
    #endif
    return 0;        
}
```

1.  无需用数组保存各个用例的结果，在用例间直接输出就可以
2.  注意状态的改变或者标记是在找到子递归的时候进行还是每次进入新递归之后进行
3.  如果需要知道 dfs 的次数，那么应该在每次进行 dfs 的时候传递步数（如 POJ 3009）