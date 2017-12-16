---
title: CSS3 transition实现进度条动画
id: 379
date: 2017-12-10 23:06:28
tags:
- CSS
categories:
- CSS
---
## 目的
实现一个类似下面所示的动画，当鼠标点击按钮时，进度条会变成不同的长度，长度相应的百分比需从服务器获取。
<html>
<iframe style="width: 100%; height: 100px" src="http://sandbox.runjs.cn/show/fhm14cl9" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
</html>
## 实现

原本打算使用CSS3的`animation`，后来发现并不知道如何用JS改变`@keyframe`中的参数。于是想起之前在犀牛书看过的`transition`，马上翻书来看，大功告成。

## 关键代码
```
.bar {
  width: 0;
  transition: width 1s ease-in;
}
```
将其初始宽度设为0，使用`transiton`规定其宽度变化时会发生的行为。待到从服务器获取到参数后，再将其宽度设为相应数字，便可得到想要的效果。
