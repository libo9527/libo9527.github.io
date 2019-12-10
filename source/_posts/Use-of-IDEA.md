---
title: Use of IDEA
description: IDEA 的使用
date: 2019-01-05 15:01:09
categories:
 - Tools
tags:
 - IDEA
---

<img src="https://www.paradigmadigital.com/wp-content/uploads/2017/08/intellijVSeclipse.jpg" width="100%"/>

<!-- more -->

## 设置

### 字体

> [IntelliJ IDEA 调整字体大小- 赱向远方- CSDN博客](https://blog.csdn.net/zhaoyayua/article/details/46548613)



### Maven

> [IDEA新建MAVEN项目时速度缓慢](https://blog.csdn.net/qq_30551211/article/details/51277444)

​	IDEA 根据 maven archetype 的本质，其实是执行 `mvn archetype:generate` 命令，该命令执行时，需要指定一个`archetype-catalog.xml`文件。

​	该命令的参数`-DarchetypeCatalog`，可选值为：remote，internal  ，local等，用来指定archetype-catalog.xml文件从哪里获取。默认为remote，即从 http://repo1.maven.org/maven2/archetype-catalog.xml 路径下载`archetype-catalog.xml`文件。http://repo1.maven.org/maven2/archetype-catalog.xml 文件约为3-4M，下载速度很慢，所以导致创建过程缓慢。

​	可以在 `settings>Building,Exceptiong,Deployment>Maven>Runner` 中配置 `VM Options` 来指定使用本地文件。如下图所示：

![](https://upload-images.jianshu.io/upload_images/130410-06580ab3367355e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

## 推荐做法

1. 项目、模块等命名时，尽量使用下划线（避免用横杠）来连接多个单词。

## Mac OS 下

### 快捷键

#### 跳转到某行/列

`Command + L`

#### 添加 MyBatis 的 XML 文件模版

1. Preference --> Editor --> File and Code Templates

   ![](https://oscimg.oschina.net/oscnet/ea4ccc152ffe4c2ee0800aa8c9feaa61b7a.jpg)

   模版内容：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
     PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
     "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="">
   
   </mapper>
   ```

2. 创建时选用 MybatisMapper 模版