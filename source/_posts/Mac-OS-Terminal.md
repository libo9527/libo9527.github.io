---
title: Mac-OS-Terminal
description: "Mac 终端的使用"
date: 2019-07-31 15:28:31
categories: Mac
tags: Command-line
---

<img src="https://photos5.appleinsider.com/gallery/29497-47785-000-3x2-Terminal-part-2-l.jpg" width="100%"/>

<!-- more -->

## 文件权限

> [第六章、Linux 的文件权限与目录配置](http://cn.linux.vbird.org/linux_basic/0210filepermission.php)

### 查看文件权限

命令：`ls -l`

```bash
$ ls -l xxx.xxx
-rw-r--r--@ 1 boris  staff  1080  7 31 18:00 xxx.xxx
```

| 文件类型和访问权限 | 文件数量 | 所属用户 | 所在群组 | 文件大小 | 修改日期（月 日 时 分） | 文件名称 |
| ------------------ | -------- | :------: | -------- | -------- | ----------------------- | -------- |
| -rw-r--r--@        | 1        |  boris   | staff    | 1080     | 7 31 18:00              | xxx.xxx  |

#### 文件类型和访问权限

第一个符号代表文件类型：

- `-` 表示该文件是非目录类型
- `d` 表示目录类型
- 中间九个字母分为三组，分别对应用户（user）、所在组（group）和其他用户（other）对该文件的访问权限。每组中的三个字符 “rwx” 分别表示对应用户对该文件拥有的可读／可写／可执行权限，没有相应权限则使用 “-” 符号替代。
- 末尾的 @ 符号表示文件拓展属性，属于文件系统的一个功能。

### 修改文件权限

命令：`chmod [用户][操作][权限] [文件]`

- 用户部分：使用字母 u 表示文件拥有者（user），g 表示拥有者所在群组（group），o 表示其他用户（other），a 表示全部用户（all，包含前面三种用户范围）；
- 操作部分：“+” 符号表示增加权限，“-” 符号表示取消权限，“=” 符号表示赋值权限；
- 权限部分：“r” 符号表示可读（read），“w” 表示可写（write），“x” 表示可执行权限（execute）；
- 文件部分：如不指定文件名，表示操作对象为当前目录下的所有文件。

```shell
$ chmod a-w demo.txt # 所有用户去掉‘写’权限
$ ls -l demo.txt 
-r--r--r--  1 boris  staff  4  8  7 09:10 demo.txt
```

