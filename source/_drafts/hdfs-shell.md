---
title: HDFS shell命令学习
id: 372
date: 2017-06-23 15:52:35
tags:
---

**appendToFile**

hdfs dfs -appendToFile &lt;localsrc&gt; ... &lt;dst&gt;

将一个或多个localsrc文件的内容追加到dst文件的末尾，如果dst所指示的文件不存在则创建一个新的。如果localsrc为-，从标准输入接受内容。

如已上传的文件tf1.txt的内容为：This is a test file. :)

本地的文件tf2.txt的内容为：This is second test file.

执行后的结果为：

**![](http://www.carolunar.com/wp-content/uploads/2017/06/552c370c8dab5043c09a90b38aa175aa.png)**

**cat**

hdfs dfs -cat URI [URI ...]

将一个或多个文件的内容显示到标准输出

**![](http://www.carolunar.com/wp-content/uploads/2017/06/b0777f14d49f6dd40f4d5e811cc43dcb.png)**

**checksum**

hdfs dfs –checksum &lt;src&gt;

显示文件校验和

**![](http://www.carolunar.com/wp-content/uploads/2017/06/22cf5702351ffbb37fb33bf4f70aad1d.png)chgrp**

hdfs dfs -chgrp [-R] GROUP URI [URI ...]

改变文件所属的组

-R 递归地改变该目录下所有文件的分组

**chmod**

hdfs dfs -chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; URI [URI ...]

改变文件的访问权限，可以用数字来表示权限。chmod abc file，其中a，b，c各为一个数字，分别表示User，Group，Other的权限。r=4，w=2，x=1，如rwx为4+2+1=7，rw-属性为4+2=6，r-x为4+1=5。

-R 递归地改变该目录下所有文件的访问权限

**![](http://www.carolunar.com/wp-content/uploads/2017/06/7c907d31ace7b3e2ba7d75cfa1b7f62d.png)注：以下使用****alias h='hdfs dfs'****简化命令**

**chown**

hdfs dfs -chown [-R] [OWNER][:[GROUP]] URI [URI]

改变文件所属的用户和分组

-R 递归地改变该目录下所有文件所属的用户和分组

**copyFromLocal**

hdfs dfs -copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;

从本地复制一个文件到hdfs

-f 如果dst所指的文件已存在，则覆盖

**![](http://www.carolunar.com/wp-content/uploads/2017/06/3988b0ffd42ccd4a847b513290a01e8d.png)copyToLocal**

hdfs dfs -copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;

从hdfs复制一个文件到本地

-ignorecrc 复制CRC校验失败的文件

-crc 复制文件以及CRC信息

目标路径必须为本地的一个文件

**count**

hdfs dfs -count [-q] [-h] &lt;path&gt;

计算指定路径下子目录和文件的数量和大小，显示出的4个字段分别为：目录数量（最小为1？），文件数量，大小，当前文件（夹）名

-h 将文件大小显示为程序员友好的格式

-q 查看限额使用情况

**![](http://www.carolunar.com/wp-content/uploads/2017/06/ed00dfe273f6ebff2e9356b376c1e80b.png)**

**cp**

hdfs dfs -cp [-f] [-p | -p[topax]] &lt;src&gt; ... &lt;dst&gt;

从src复制文件到dst，如果复制多个文件，dst必须为一个文件夹。

-p 保存文件状态

-f 如果dst已存在，覆盖

**![](http://www.carolunar.com/wp-content/uploads/2017/06/d2dd3175d1a2018d86ddd039b9f36232.png)createSnapshot**

hdfs dfs -createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]

创建指定目录的一个快照，即在某一时刻的镜像

**deleteSnapshot**

hdfs dfs -deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;

删除某个镜像

**df**

hdfs dfs -df [-h] [&lt;path&gt; ...]

查看指定目录下磁盘空间的大小和使用情况

-h 将大小显示为便于阅读的格式

**![](http://www.carolunar.com/wp-content/uploads/2017/06/7ec0bce67ee7440384dd64fcfe46bd76.png)**

**du**

hdfs dfs -du [-s] [-h] &lt;path&gt;

统计指定目录下各个文件的大小

-s 显示所有文件的总和而不是分别显示

**![](http://www.carolunar.com/wp-content/uploads/2017/06/44a6256c676046c065f270d6142644fd.png)expunge**

hdfs dfs –expunge

清空回收站的内容（然而并没有找到回收站/trash）

**get**

hdfs dfs -get [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;

类似于copyToLocal，从hdfs复制文件到本地路径。可以复制多个，此时本地路径必须为一个文件夹。

![](http://www.carolunar.com/wp-content/uploads/2017/06/70652bf8d08d5b26a338bb792889597e.png)

**getfacl **

hdfs dfs -getfacl [-R] &lt;path&gt;

显示指定目录的访问控制列表，包括其所属用户，分组，用户和分组的权限

**![](http://www.carolunar.com/wp-content/uploads/2017/06/a21892ae89715f852d08a2975e8a5cf6.png)**

**getfattr**

hdfs dfs -getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;

显示指定文件或目录的其他属性，如果有的话

**getmerge**

hdfs dfs -getmerge [-nl] &lt;src&gt; &lt;localdst&gt;

将src制定的多个文件合并为localdst指定的一个文件，方便查看

-nl 在每个文件末尾加一个空行

**![](http://www.carolunar.com/wp-content/uploads/2017/06/a21892ae89715f852d08a2975e8a5cf6.png)**

**ls**

hdfs dfs -ls [-d] [-h] [-R] [&lt;path&gt; ...]

列出指定目录下的文件

-d 列出目录自身的属性，而非其中的内容

-h 将文件大小转变为可读性强的格式

-R 递归地列出所有子目录和文件的信息

**![](http://www.carolunar.com/wp-content/uploads/2017/06/29510902f00b8b88f6f16d0378abb25f.png)mkdir**

hdfs dfs -mkdir [-p] &lt;path&gt;

在制定目录下创建文件夹

-p 如果该目录已存在，则不报错。如果父目录不存在，则强行创建

![](http://www.carolunar.com/wp-content/uploads/2017/06/e58010f7319fcb93192b3fc3b1c865d4.png)

**moveFromLocal**

hdfs dfs -moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;

剪切本地文件到hdfs中

**mv**

hdfs dfs -mv &lt;src&gt; ... &lt;dst&gt;

从src移动文件到dst，如果移动多个文件，则dst必须为文件夹。

![](http://www.carolunar.com/wp-content/uploads/2017/06/9d9f85f230c0737bcbfa62f4abf380e2.png)

**put**

hdfs dfs -put [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;

同copyFromLocal类似

**renameSnapshot**

hdfs dfs -renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;

将指向snapshotDir的快照重命名，从oldName改为newName

**rm**

hdfs dfs -rm [-f] [-r|-R] [-skipTrash] &lt;src&gt;

删除hdfs的文件。

-f 如果要删除的文件不存在，不报错

-r|-R 递归地删除其所有子目录及文件

**![](http://www.carolunar.com/wp-content/uploads/2017/06/6d76abf247822f91e810fb224193c2e8.png)**

**setrep**

hdfs dfs -setrep [-R] [-w] &lt;rep&gt; &lt;path&gt;

改变文件的副本个数，如果path是文件夹则递归地改变其内所有子文件的副本个数。如果大于机器个数，实际还是机器个数。

-w 命令行需等待任务完成，可能需要很长时间

**![](http://www.carolunar.com/wp-content/uploads/2017/06/d88575de34a556782388380d9fddc6dd.png)stat**

hdfs dfs -stat [format] &lt;path&gt;

以特定格式显示指定目录或文件的状态

%b 文件大小

%g 所属分组

%n 目录名

%o 块大小

%r 副本数

%u 所属用户

%y, %Y 修改时间

** ![](http://www.carolunar.com/wp-content/uploads/2017/06/ab453c8a3b59fd7553cdcada481336ef.png)**

**tail **

-tail [-f] &lt;file&gt;

显示指定文件的最后一个字节的内容

-f 文件增加时显示增加的数据

![](http://www.carolunar.com/wp-content/uploads/2017/06/26a5753e0aa642da317999b7069d1023.png)

**![](http://www.carolunar.com/wp-content/uploads/2017/06/3f0727dae84f568cbc82c64f28f5ff4a.png)**

**![](http://www.carolunar.com/wp-content/uploads/2017/06/4ced21e270261be5dc2fb151170a4106.png)**

**text**

hdfs dfs -text [-ignoreCrc] &lt;src&gt;

将文件以文本的形式显示

**![](http://www.carolunar.com/wp-content/uploads/2017/06/4bcb472bf56b09da701933de644a16e8.png)**

**touchz**

hdfs dfs -touchz &lt;path&gt;

创建一个大小为0的空文件

**![](http://www.carolunar.com/wp-content/uploads/2017/06/af1c9faedbcc1a0537982ffc202bdc8d.png)usage**

hdfs dfs -usage [cmd ...]

显示指定命令的用法