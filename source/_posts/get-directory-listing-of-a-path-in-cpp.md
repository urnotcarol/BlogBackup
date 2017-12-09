---
title: C++ 获取特定路径下所有文件的文件名
id: 264
date: 2017-03-21 16:31:53
tags:
---

<pre class="lang:default decode:true  ">#include <iostream>
#include <io.h>
#include <string>
#include <vector>
using namespace std;

void getFiles(string path, vector<string>&amp; fileName) {
    intptr_t hFile = 0;
    _finddata_t fileInfo;
    string p;
    if ((hFile = _findfirst(p.assign(path).append("\\*").c_str(), &amp;fileInfo)) != -1) {
        do {
            if (fileInfo.attrib &amp; _A_SUBDIR) {
                if (strcmp(fileInfo.name, ".") != 0 &amp;&amp; strcmp(fileInfo.name, "..") != 0) {
                    getFiles(p.assign(path).append("\\").append(fileInfo.name), fileName);
                }
            }
            else {
                fileName.push_back(p.assign(path).append("\\").append(fileInfo.name));
            }
        } while (_findnext(hFile, &amp;fileInfo) == 0);
        _findclose(hFile);
    }
}

int main() {
    string path = "testDir";
    vector<string> fileName;
    getFiles(path, fileName);
    for (int i = 0; i &lt; fileName.size(); i++) {
        cout &lt;&lt; fileName[i] &lt;&lt; endl;
    }
    return 0;
}
```

&nbsp;