---
title: '[转] AOJ 0525 Osenbei《挑战程序设计竞赛(第2版)》练习题答案'
id: 102
date: 2017-02-12 22:00:07
tags:
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
    while(cin &gt;&gt; R &gt;&gt; C &amp;&amp; R &gt; 0)
    {
        int i, j;
        for (i = 0; i &lt; R; ++i)
        {
            for (j = 0; j &lt; C; ++j)
            {
                bool upwards;
                cin &gt;&gt; upwards;
                cookie[i][j] = upwards;
            }
        }

        // 在横向一共有2^R种变换
        int permute_r = 1 &lt;&lt; R;
        int result = 0;
        for (i = 0; i &lt; permute_r ; ++i)
        {
            // 完成当前的变换
            for (j = 0; j &lt; R; ++j)
            {
                // 这一行是否应当翻个面
                if (i &amp; (1 &lt;&lt; j))
                {
                    cookie[j].flip();
                }
            }

            // 对每一列分别算出朝上和朝下的煎饼个数，取其最大值
            int possible_answer = 0;
            for (j = 0; j &lt; C; ++j)
            {
                int up_cookie_count = 0;
                for (int k = 0; k &lt; R; ++k)
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
            for (j = 0; j &lt; R; ++j)
            {
                if (i &amp; (1 &lt;&lt; j))
                {
                    cookie[j].flip();
                }
            }
        }
        cout &lt;&lt; result &lt;&lt; endl;
    }

    return 0;
}
```

____________________总结的分割线____________________

1.  原博有一个很妙的写法，permute = 2^N，循环 permute 次，每次又根据 (i &amp; (1 &lt;&lt; j)) 为真来确定特定要翻转的煎饼，这样，用二重循环加一个if判断就可以代替任意N重循环。【手动插入“你太强啦”表情包】
2.  bitset是个好东西，尖括号里写的不是数据类型，而是二进制的位数。
3.  原博的写法是先定义了 int i,j ，以后的每次循环都直接用，和我的习惯不一样。我照着原博代码写的时候就发现了一个问题，经过测试如下：

```
#include <iostream>using namespace std;

    int main() {
    int i;
    for (i = 0; i &lt; 3; i++) {
        for (i = 0; i &lt; 5; i++) {
            cout <&lt; i;  //-->01234
        }
    }
    return 0;
}
```

```\n#include <iostream>using namespace std;

    int main() {
    for (int i = 0; i &lt; 3; i++) {
        for (int i = 0; i &lt; 5; i++) {
            cout <&lt; i; //-->012340123401234
        }
    }
    return 0;
}
```


因此决定以后还是直接在for循环内定义变量，虽然麻烦点，但是减少了出错概率，以及不用想那么多变量名。