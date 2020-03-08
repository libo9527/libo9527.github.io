---
title: Study Notes of Shell
description: Shell 学习笔记
date: 2020-03-07 15:34:22
categories:
- Study Notes
- Shell
tags: Shell
---

<img src="https://www.runoob.com/wp-content/uploads/2015/12/post-shell-script.jpg" width="100%"/>

<!-- more -->

>https://www.yinwang.org/blog-cn/2013/03/29/scripting-language
>
>https://www.learnshell.org/
>
>https://www.tutorialspoint.com/unix/shell_scripting.htm
>
>http://www.freeos.com/guides/lsst/
>
>https://linuxconfig.org/bash-scripting-tutorial-for-beginners
>
>https://bash.cyberciti.biz/guide/Main_Page
>
>https://book.douban.com/subject/26854226/
>
>https://coding.imooc.com/class/314.html
>

## Shell脚本编程30分钟入门

> https://github.com/qinjx/30min_guides/blob/master/shell.md

### 环境

shell 编程只需要一个编写代码的**文本编辑器**和一个能解释执行的**脚本解释器**就可以了。

#### OS

当前主流的操作系统都支持 shell  编程，本文档所述的shell 编程是指 Linux 下的 shell，讲的基本都是POSIX标准下的功能，所以，也适用于Unix及BSD（如Mac OS）。

##### Linux

Linux 默认自带 shell 解释器。

##### Mac OS

Mac OS不仅带了sh、bash这两个最基础的解释器，还内置了ksh、csh、zsh等不常用的解释器。

##### Windows

windows 出厂时没有内置 shell 解释器，需要自行安装，为了同时能用 grep, awk, curl 等工具，最好装一个 cygwin或者 mingw 来模拟linux环境。

- [cygwin](http://www.cygwin.com/)
- [mingw](http://www.mingw.org/)

#### 脚本解释器

##### sh

即 Bourne shell，POSIX(Portable Operating System Interface)标准的 shell 解释器，它的二进制文件路径通常是 /bin/sh，由Bell Labs开发。

##### bash

Bash 是 Bourne shell 的替代品，属 GNU Project，二进制文件路径通常是 /bin/bash。业界通常混用 bash、sh 和 shell，比如你会经常在招聘运维工程师的文案中见到：熟悉 Linux Bash 编程，精通 Shell 编程。

在CentOS里，/bin/sh是一个指向/bin/bash的符号链接:

```shell
[root@centosraw ~]# ls -l /bin/*sh
-rwxr-xr-x. 1 root root 903272 Feb 22 05:09 /bin/bash
-rwxr-xr-x. 1 root root 106216 Oct 17  2012 /bin/dash
lrwxrwxrwx. 1 root root      4 Mar 22 10:22 /bin/sh -> bash
```

但在Mac OS上不是，/bin/sh和/bin/bash是两个不同的文件，尽管它们的大小只相差100字节左右:

```shell
iMac:~ wuxiao$ ls -l /bin/*sh
-r-xr-xr-x  1 root  wheel  1371648  6 Nov 16:52 /bin/bash
-rwxr-xr-x  2 root  wheel   772992  6 Nov 16:52 /bin/csh
-r-xr-xr-x  1 root  wheel  2180736  6 Nov 16:52 /bin/ksh
-r-xr-xr-x  1 root  wheel  1371712  6 Nov 16:52 /bin/sh
-rwxr-xr-x  2 root  wheel   772992  6 Nov 16:52 /bin/tcsh
-rwxr-xr-x  1 root  wheel  1103984  6 Nov 16:52 /bin/zsh
```

#### ~~运行~~



## 归纳总结

### 指定脚本解释器

“#!”是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行。

### 运行

运行 Shell 脚本有两种方法。

#### 作为可执行程序

1. 赋予可执行权限

   ```shell
   chmod +x test.sh
   ```

2. 运行

   ```shell
   test.sh
   ```

#### 作为解释器参数

这种运行方式是直接运行解释器，其参数就是shell脚本的文件名，如：

```shell
/bin/sh test.sh
```

这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。

