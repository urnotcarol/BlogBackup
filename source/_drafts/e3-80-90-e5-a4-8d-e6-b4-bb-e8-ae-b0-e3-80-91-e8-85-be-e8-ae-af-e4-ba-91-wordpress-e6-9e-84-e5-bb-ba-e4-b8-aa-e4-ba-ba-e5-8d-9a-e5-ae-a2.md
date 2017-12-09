---
title: 【复活记】腾讯云 + WordPress 构建个人博客
id: 212
tags:
---

说明：这个教程主要模仿腾讯官方的[这篇教程](https://www.qcloud.com/community/article/175)写成，有一个步骤的改动，主要的目的是——省钱，咩哈哈。

1\. 购买一台云主机并登录

1.1 购买云主机，注意镜像选择 Ubuntu Server 14.04.1 LTS 64 位，因为后面需要用到 php5，而 16.04 版本的 Ubuntu 内置的是 php7，有一点麻烦~~~

学生可以完成学生认证后[领取优惠券](https://www.qcloud.com/act/campus)，只要几块钱就可以买到云主机&lt;(￣︶￣)&gt;

1.2 登陆

可以直接在网站上的云主机页面登录，也可以下载并安装 [Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)，在自己电脑上远程登录。

![](http://118.89.242.250/wp-content/uploads/2017/03/QQ图片20170314152119.png)

点击 open, 使用账号 ubuntu 和购买云主机时设置的密码，就登录成功啦。

2\. 配置环境
<pre class="theme:solarized-light font-size:14 lang:sh decode:true ">apt-get install mysql-server mysql-client nginx php5-fpm php5-mysql</pre>
&nbsp;