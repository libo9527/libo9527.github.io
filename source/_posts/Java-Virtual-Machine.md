---
title: Java Virtual Machine
description: Java 虚拟机
comments: false
hidden: false
top: false
date: 2020-10-26 14:14:00
categories:
tags:
---

<img src="https://static001.infoq.cn/resource/image/5f/5e/5fe5dc2c6950ce2dce8808da91b63c5e.png" width="100%"/>

<!-- more -->

## 笔试题

### Java 内存泄露的排查（思路及过程）

> [Java内存泄漏的排查总结](https://blog.csdn.net/fishinhouse/article/details/80781673)
>
> [JVM第一篇：一个Java内存泄漏的排查案例](https://cloud.tencent.com/developer/article/1144256)

1. 使用 jps/ps 命令确定虚拟机进程ID(LVMID，Local Virtual Machine Identifier)
2. 使用 jstat 命令查看 JVM 的 GC 情况
3. 使用 jmap 命令生产堆快照 dump 文件
4. 使用 MAT 工具分析 dump 文件，在 MAT 中查看 Action 窗口下的 Histogram，找到占用内存大的对象，分析引用栈，最后定位到具体代码