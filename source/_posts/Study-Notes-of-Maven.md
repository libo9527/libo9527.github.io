---
title: Study Notes of Maven
description: 一句话描述这篇文章
comments: false
hidden: false
top: false
date: 2020-03-20 18:52:17
categories:
tags:
---

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Maven_logo.svg/1280px-Maven_logo.svg.png" width="100%"/>

<!-- more -->

## 资源文件

> [maven资源文件的相关配置- XRacoon - 博客园](https://www.cnblogs.com/pixy/p/4798089.html)
>
> [解决springboot配置文件yml中classpath找不到src/main/java下 ...](https://blog.csdn.net/qq_41357211/article/details/99855643)

构建Maven项目的时候，如果没有进行特殊的配置，Maven会按照标准的目录结构查找和处理各种类型文件。



src/main/java和src/test/java 

这两个目录中的所有*.java文件会分别在comile和test-comiple阶段被编译，编译结果分别放到了target/classes和targe/test-classes目录中，但是这两个目录中的其他文件都会被忽略掉。



src/main/resouces和src/test/resources

这两个目录中的文件也会分别被复制到target/classes和target/test-classes目录中。



target/classes

打包插件默认会把这个目录中的所有内容打入到jar包或者war包中。

