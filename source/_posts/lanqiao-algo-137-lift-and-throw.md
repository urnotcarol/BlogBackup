---
title: 蓝桥杯 ALGO-137 Lift and Throw 题解
id: 275
date: 2017-04-02 14:37:15
tags:
- 算法
- OJ
categories:
- 算法
- 搜索
---

## 题目
> <div class="pdsec">问题描述
> 
> <div class="pdcont">　　给定一条标有整点(1, 2, 3, ...)的射线. 定义两个点之间的距离为其下标之差的绝对值.
> 
> Laharl, Etna, Flonne一开始在这条射线上不同的三个点, 他们希望其中某个人能够到达下标最大的点.
> 
> 每个角色只能进行下面的3种操作, 且每种操作不能每人不能进行超过一次.
> 
> 1.移动一定的距离
> 
> 2.把另一个角色高举过头
> 
> 3.将举在头上的角色扔出一段距离
> 
> 每个角色有一个movement range参数, 他们只能移动到没有人的位置, 并且起点和终点的距离不超过movement range.
> 
> 如果角色A和另一个角色B距离为1, 并且角色B没有被别的角色举起, 那么A就能举起B. 同时, B会移动到A的位置,B原来所占的位置变为没有人的位置. 被举起的角色不能进行任何操作, 举起别人的角色不能移动.同时, 每个角色还有一个throwing range参数, 即他能把举起的角色扔出的最远的距离. 注意, 一个角色只能被扔到没有别的角色占据的位置. 我们认为一个角色举起另一个同样举起一个角色的角色是允许的. 这种情况下会出现3个人在同一个位置的情况. 根据前面的描述, 这种情况下上面的两个角色不能进行任何操作, 而最下面的角色可以同时扔出上面的两个角色. 你的任务是计算这些角色能够到达的位置的最大下标, 即最大的数字x, 使得存在一个角色能够到达x.
> 
> <div class="pdsec">输入格式
> 
> <div class="pdcont">　　输入共三行, 分别为Laharl, Etna, Floone的信息.
> 
> 每一行有且仅有3个整数, 描述对应角色的初始位置, movement range, throwing range.
> 
> 数据保证3个角色的初始位置两两不相同且所有的数字都在1到10之间.
> 
> <div class="pdsec">输出格式
> 
> <div class="pdcont">　　仅有1个整数, 即Laharl, Etna, Flonne之一能到达的最大距离.
> 
> <div class="pdsec">样例输入
> 
> <div class="pddata">9 3 3
> 
> 4 3 1
> 
> 2 3 3
> 
> <div class="pdsec">样例输出
> 
> <div class="pddata">15
> 
> <div class="pdsec">样例说明
> 
> <div class="pdcont">　　一开始Laharl在位置9, Etna在位置4, Flonne在位置2.
> 
> 首先, Laharl移动到6.
> 
> 然后Flonne移动到位置5并且举起Etna.
> 
> Laharl举起Flonne将其扔到位置9.
> 
> Flonne把Etna扔到位置12.
> 
> Etna移动到位置15.
## 思路

这道题跟之前做过的 [Curling 2.0](http://www.carolunar.com/poj-3009-curling-2-0/) 是同样的思路，用 DFS + 回溯去遍历所有可能的状态。


<!-- more -->
## 代码
```
#include <iostream>
#include <algorithm>
#include <stdio.h>
#include <math.h>

using namespace std;

struct State {
    bool hasMoved;
    bool hasLifted;
    bool canThrow;
    bool able;
    State() {
        hasMoved = false;
        hasLifted = false;
        canThrow = false;
        able = true;
    }
}; 
State s[4];

int pos[4]; //current position
int mr[4];  //move range
int tr[4];  //throw range
int p[4];   //lift which one
int res = 0;

bool near(int s) {
    for (int i = 1; i <= 3; i++) {
        if (abs(pos[i] - s) == 1) {
            return true;
        }
    }
    return false;    
}

void dfs() {
    for (int i = 1; i <= 3; i++) {
        res = max(res, pos[i]);
    } 
    for (int i = 1; i <= 3; i++) {
        if (s[i].able == false) {
            continue;
        }

        if (s[i].hasMoved == false && s[i].canThrow == false) {
            for (int j = -mr[i]; j <= mr[i]; j++) {
                if (j == 0) {
                    continue;
                }
                pos[i] += j;
                s[i].hasMoved = true;
                if (near(pos[i]) || j == mr[i] || j == -mr[i]) {
                    dfs();
                }
                pos[i] -= j;
                s[i].hasMoved = false;               
            }
        }  

        if (s[i].hasLifted == false) {
            for (int j = 1; j <= 3; j++) {
                if (s[j].able == true && abs(pos[i] - pos[j]) == 1) {
                    s[j].able = false;
                    s[i].hasLifted = true;
                    s[i].canThrow = true;
                    p[i] = j;
                    int temp = pos[j];
                    dfs();
                    pos[j] = temp;
                    s[i].canThrow = false;
                    s[i].hasLifted = false;
                    s[j].able = true; 
                }
            }
        }

        if (s[i].canThrow) {
            for (int j = -tr[i]; j <= tr[i]; j++) {
                if (j == 0) {
                    continue;
                }
                s[p[i]].able = true;
                s[i].canThrow = false;
                int temp = pos[p[i]];
                pos[p[i]] = pos[i] + j;
                if (near(pos[p[i]]) || j == -tr[i] || j == tr[i]) {
                    dfs();
                }
                pos[p[i]] = temp;
                s[i].canThrow = true;
                s[p[i]].able = false;                   
            }
        }
    }
    return;
}

int main() {
    for (int i = 1; i <= 3; i++) {
        scanf("%d %d %d", &pos[i], &mr[i], &tr[i]);
    }
    dfs();  
    cout << res;
    return 0;    
}


```

<table class="table table-hover table-striped" cellspacing="0px" cellpadding="0px">
<tbody>
<tr>
<td><span style="font-size: 8pt;">1604924</span></td>
<td><span style="font-size: 8pt;">^ ^</span></td>
<td class="pname"><span style="font-size: 8pt;">[Lift and Throw](http://lx.lanqiao.cn/problem.page?gpid=T356)</span></td>
<td><span style="font-size: 8pt;">04-02 14:23</span></td>
<td><span style="font-size: 8pt;">2.572KB</span></td>
<td><span style="font-size: 8pt;">C++</span></td>
<td><span class="Color_AC" style="font-size: 8pt;">正确</span></td>
<td><span style="font-size: 8pt;">100</span></td>
<td><span style="font-size: 8pt;">1.187s</span></td>
<td><span style="font-size: 8pt;">940.0KB</span></td>
</tr>
</tbody>
</table>
&nbsp;

&nbsp;

## 参考

* * *

[算法训练 Lift and Throw (DFS && 位运算)](http://blog.csdn.net/f_zyj/article/details/50867004)