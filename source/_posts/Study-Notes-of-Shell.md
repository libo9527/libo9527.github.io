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

### 变量

#### 定义

定义变量时，变量名**不**加美元符号 `$`，如：

```shell
your_name="xxx"
```

注意，变量名和等号之间**不**能有空格，这可能和你熟悉的所有编程语言都不一样。

#### 调用

调用一个定义过的变量，只要在变量名前面加美元符号 `$` 即可，如：

```shell
your_name="xxx"
echo $your_name
echo ${your_name}
```

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，比如下面这种情况：

```shell
for skill in Ada Coffe Action Java; do
	echo "I am good at ${skill}Script"
done
```

如果不给skill变量加花括号，写成 `echo "I am good at $skillScript"`，解释器就会把​`$skillScript` 当成一个变量，代码执行结果就不是我们期望的样子了。

推荐给所有变量加上花括号，这是个好的编程习惯。IntelliJ IDEA 编写 shell script 时，会提示加花括号。

### 注释

以 `#` 开头的行就是注释，会被解释器忽略。

#### 多行注释

sh 里没有多行注释，只能每一行加一个 # 号。

如果在开发过程中，遇到大段的代码需要临时注释起来，过一会儿又取消注释，怎么办呢？每一行加个 # 符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果。

### 字符串

字符串是 shell 编程中最常用的数据类型，字符串可以用单引号，也可以用双引号，也可以不用引号。单双引号的区别跟 PHP 类似。

#### 单引号

```shell
str='this is a string'
```

单引号字符串的限制：

- 单引号里的**任何**字符都会原样输出，单引号字符串中的变量是**无效**的
- 单引号字串中不能出现单引号（对单引号使用转义符后**也**不行）

#### 双引号

```shell
your_name='xxx'
str="Hello, I know your are \"$your_name\"! \n"
```

- 双引号里可以有变量
- 双引号里可以出现转义字符

#### 字符串操作

##### 拼接字符串

```shell
your_name="xxx"
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"

echo $greeting $greeting_1
```

##### 获取字符串长度

语法：<code><span>${</span><span>#string}</span></code>

```shell
string="abcd"
echo ${#string} #输出：4
```

##### 提取子字符串

语法：
1. `${string:position}`：在 `$string` 中, 从位置 `$position` 开始提取子串
2. `${string:position:length}`：在 `$string` 中, 从位置`$position` 开始提取长度为 `$length` 的子串

```shell
string="alibaba is a great company"
echo ${string:1:4} #输出：liba
```

##### 查找字符索引

语法：`expr index "$string" c`

1. 查找结果的是第一个符合条件的字符所在的位置
2. 这里的索引并不是从0开始，而是从1开始算起，如果返回0，则表示查找失败
3. 当 `c` 是一个字符串时，结果是遍历 `$string` 的每个字符，当该字符在 `c` 中时，返回其位置。

```shell
string="alibaba is a great company"
echo `expr index "$string" i`
#输出：3，这个语句的意思是：找出字母 i 在这句话中的位置，要在linux下运行，mac下会报错

echo `expr index "$string" si`
#输出：3，因为在 $string 中先出现 si 中的 i，其位置是3
```

