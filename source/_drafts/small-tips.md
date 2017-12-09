---
title: 一些零碎的小笔记
id: 364
date: 2017-06-06 01:03:42
tags:
---

Linux命令
<div>

*   add-apt-repository 是由 [Python](http://lib.csdn.net/base/python "Python知识库")-software-properties 这个工具包提供的,只有ubuntu 0910之后版本支持。可以把**命令源**添加到/etc/apt/sources.list中。
> # 我们先了解一下 PPA 的定义： PPA 全称为 Personal Package Archives（个人软件包档案），是 Ubuntu Launchpad 网站提供的一项服务，当然不仅限于 Launchpad 。它允许个人用户上传软件源代码，通过 Launchpad 进行编译并发布为二进制软件包，作为 apt/新立得源供其他用户下载和更新。在Launchpad网站上的每一个用户和团队都可以拥有一个或多个PPA。 通常 PPA 源里的软件是官方源里没有的，或者是最新版本的软件。相对于通过 Deb 包安装来说，使用 PPA 的好处是，一旦软件有更新，通过 sudo apt-get upgrade 这样命令就可以直接升级到新版本。 # 如何通过 PPA 源来安装软件： 通常我们可以通过 Google 来搜索一些常用软件的 PPA 源，通常的搜索方法是软件名称关键字 + PPA ，或者也可直接到 launchpad.net 上去搜索，搜索到后我们就可以直接用 sudo apt-add-repository 命令把 PPA 源添加到 Source list 中了。 比如 FireFox PPA 源：https://launchpad.net/~ubuntu-mozilla-daily/+archive/ppa ，我们可以在这里找到 ppa:ubuntu-mozilla-daily/ppa 的字样，然后我们通过以下命令把这个源加入到 source list 中。 sudo apt-add-repository ppa:ubuntu-mozilla-daily/ppa 然后我们再从下面的 Packages 列表中找到适用于当前 Ubuntu 版的 FireFox 4.0 包名称，更新源并安装： sduo apt-get update sudo apt-get install firefox-4.0 事实上除了命令行方式外，Ubuntu 还提供了 GUI 界面的第三方源管理工具，这个就不详述了。另外， Ubuntu-Tweak 中也包含了大量第三方源，可以通过一键安装第三方软件，强烈建议新手安装。
</div>

*   <span class="i">apt</span><span class="o">-</span><span class="e">key </span><span class="i">add</span> <span class="o">&lt;</span><span class="r">file</span><span class="o">&gt;</span><span class="h">          </span><span class="o">-</span> 把下载的<span class="i">key</span>添加到本地<span class="i">trusted</span>数据库中
*   如果使用`--save-exact`参数，会在package.json文件指定安装模块的确切版本。
<div class="language-bash highlighter-rouge">

    <span class="gp">$ </span>npm install readable-stream --save --save-exact
    `</pre>
    </div>
*   在 npm3 中，–save、–save-dev 和 –save-optional、–save-bundle、–save-exact 都有对应的简写：
    <pre style="padding-left: 90px;">`-S: --save
    -D: --save-dev
    -O: --save-optional
    -B: --save-bundle
    -E: --save-exact  与 --save, --save-dev or --save-optional 一起使用，保证