---
title: ACdream 1056 Bad Horse 题解
id: 244
date: 2017-03-18 21:45:26
tags:
---

## 题目大意

大王有很多手下，然而其中好多人都两两不和。给定所有不共戴天的人的名单，你的任务是帮大王求出是否能把这些人完全分成两派，让那些不和的人不必见面。

## 思路

二分图的练习题。用邻接表表示图，用染色法判断是否是二分图。

<!-- more -->

## 注意

STL 容器在不同的 case 里重复利用的时候记得初始化。

## AC 代码
```
#include <iostream>
#include <stdio.h>
#include <vector>
#include <string>

using namespace std;

const int MAX_M = 100 + 4; 
int T;
int M;
struct Member {
    string name;
    vector<string> edge;
    int color;
};
vector<Member> member;

void init(string node1, string node2) {
    bool found = false;
    for (int j = 0; j &lt; member.size(); j++) {
        if (member[j].name == node1) {
            member[j].edge.push_back(node2);
            found = true;
            break;
        }
    }
    if (!found) {
        Member temp;
        temp.name = node1;
        temp.edge.push_back(node2);
        temp.color = 0;
        member.push_back(temp);
    }
}

bool dfs(int v, int c) {
    member[v].color = c;
    int index;
    for (int i = 0; i &lt; member[v].edge.size(); i++) {
        for (int j = 0; j &lt; member.size(); j++) {
            if (member[j].name == member[v].edge[i]) {
                index = j;
                break;
            }
        }
        if (member[index].color == c) {
            return false;
        }
        if (member[index].color == 0 &amp;&amp; !dfs(index, -c)) {
            return false;
        }
    }
    return true;
}

bool solve() {
    for (int i = 0; i &lt; member.size(); i++) {
        if (member[i].color == 0) {
            if (!dfs(i, 1)) {
                return false;
            }
        }
    } 
    return true;
}

int main() {
    scanf("%d", &amp;T);
    for (int t = 1; t &lt;= T; t++) {
        bool res;
        scanf("%d", &amp;M);
        for (int i = 0; i &lt; member.size(); i++) {
            member[i].edge.clear();
        }
        member.clear();
        string first;
        string second;
        for (int i = 0; i &lt; M; i++) {
            cin &gt;&gt; first &gt;&gt; second;
            init(first, second);
            init(second, first);
        }

        if (solve()) {
            printf("Case #%d: Yes\n", t);
        }    
        else {
            printf("Case #%d: No\n", t);
        }
    }
    return 0;
}
```

//0318按：据说可以用 map 直接把 string 映射成整数，会方便许多。机智的我发现自己实现了一遍 map 的初始化以及查找？暂作阙疑，以后再优化。