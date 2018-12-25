---
title: Wget for windows
date: 2018-12-24 19:01:03
tags:
 - Wget
---

> [Wget for windows——优雅地实现批量下载](https://zhuanlan.zhihu.com/p/28826000)

<!-- more -->

# 下载与安装

1. 点击[Windows binaries of GNU Wget](http://link.zhihu.com/?target=https%3A//eternallybored.org/misc/wget/)下载ZIP格式文件
2. 加压到任意目录（如`D:/APPS/wget/`）

# 配置环境变量

1. 使用快捷键`win+r`，输入`sysdm.cpl`，然后回车。
2. 选择高级>环境变量>系统变量中选中`Path`>编辑>新建>浏览>找到对应目录（如`D:/APPS/wget/`）>确定
3. 控制台输入命令`wget -V`查看版本