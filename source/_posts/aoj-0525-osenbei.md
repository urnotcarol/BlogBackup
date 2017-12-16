---
title: '[转] AOJ 0525 Osenbei《挑战程序设计竞赛(第2版)》练习题答案'
id: 102
date: 2017-02-12 22:00:07
tags:
- 算法
- OJ
categories:
- 算法
- 搜索
---

来自 [码农场](http://www.hankcs.com/) » [AOJ 0525 Osenbei《挑战程序设计竞赛(第2版)》练习题答案](http://www.hankcs.com/program/cpp/aoj-0525-osenbei-challenge-programming-contest-2nd-edition-exercises-answers.html)

只把代码复制过来，原博的其他分析请看链接。
```
#include <iostream>
#include <bitset>
#include <algorithm>

using namespace std;

bitset<10000> cookie[10];

///////////////////////////SubMain//////////////////////////////////
int main(int argc, char *argv[])
{

    int R, C;
    while(cin >> R >> C && R > 0)
    {
        int i, j;
        for (i = 0; i < R; ++i)
        {
            for (j = 0; j < C; ++j)
            {
                bool upwards;
                cin >> upwards;
                cookie[i][j] = upwards;
            }
        }

        // 在横向一共有2^R种变换
        int permute_r = 1 << R;
        int result = 0;
        for (i = 0; i < permute_r ; ++i)
        {
            // 完成当前的变换
            for (j = 0; j < R; ++j)
            {
                // 这一行是否应当翻个面
                if (i & (1 << j))
                {
                    cookie[j].flip();
                }
            }

            // 对每一列分别算出朝上和朝下的煎饼个数，取其最大值
            int possible_answer = 0;
            for (j = 0; j < C; ++j)
            {
                int up_cookie_count = 0;
                for (int k = 0; k < R; ++k)
                {
                    if (cookie[k][j])
                    {
                        ++up_cookie_count;
                    }
                }
                possible_answer += max(up_cookie_count, R - up_cookie_count);
            }
            // 结果取最大值
            result = max(result, possible_answer);

            // 复原
            for (j = 0; j < R; ++j)
            {
                if (i & (1 << j))
                {
                    cookie[j].flip();
                }
            }
        }
        cout << result << endl;
    }

    return 0;
}
```

____________________总结的分割线____________________

1.  原博有一个很妙的写法，permute = 2^N，循环 permute 次，每次又根据 (i & (1 << j)) 为真来确定特定要翻转的煎饼，这样，用二重循环加一个if判断就可以代替任意N重循环。【手动插入“你太强啦”表情包】
2.  bitset是个好东西，尖括号里写的不是数据类型，而是二进制的位数。