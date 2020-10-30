---
title: Interview
description: 面试
comments: false
hidden: false
top: false
date: 2020-10-21 20:00:51
categories:
tags:
---

<img src="https://resources.workable.com/wp-content/uploads/2018/05/prepare-interviews-featured.png" width="100%"/>

<!-- more -->

## 编程思想

### OPP

面向过程编程（英语：Procedure-oriented Programming，缩写：OPP）

### OOP

面向对象编程（英语：Object-oriented programming，缩写：OOP）

#### OPP vs OOP

面向过程的程序设计<span style="color: red">把</span>计算机程序<span style="color: red">视为</span>一系列的**命令集合**，即一组函数的顺序执行。为了简化程序设计，面向过程把函数继续切分为子函数，即把大块函数通过切割成小块函数来降低系统的复杂度。

而面向对象的程序设计<span style="color: red">把</span>计算机程序<span style="color: red">视为</span>一组**对象的集合**，而每个对象都可以接收其他对象发过来的消息，并处理这些消息，**计算机程序的执行就是一系列消息在各个对象之间传递**。

#### 三大特征

面向对象的三大特征：封装、继承、多态

##### 封装

把数据以及对数据的操作封装在一个类里。

##### 继承

##### 多态

父类引用指向子类对象。

### AOP

在软件业，AOP 为 Aspect Oriented Programming 的缩写，意为：面向切面编程

## Java 语言

### 基础

### 反射

### 序列化

### 泛型

### JVM

#### 类加载机制

##### 类的生命周期/类加载过程

类从被加载到虚拟机内存中开始，到卸载出内存为止，它的整个生命周期包括 <span style="color: red">7 个阶段</span>。其中准备、验证、解析 3 个部分统称为连接（Linking）。

加载(loading) --> 验证(Verification) -->准备(Preparation) --> 解析(Resolution) --> 初始化(Initialization) --> 使用(Using) --> 卸载(Unloading)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0f14bd8d34554d94a925fab4bbdc7911~tplv-k3u1fbpfcp-zoom-1.image)

###### 加载

将 `.class` 文件加载到 JVM 运行时数据区的**方法区**内，然后在堆中创建一个 java.lang.Class 对象，用来封装类在方法区内的数据结构。

`.class` 文件来源：

1. 本地磁盘
2. 网络下载
3. zip、jar 等归档文件中
4. 数据库
5. 动态编译 Java 源文件

###### 验证

验证的目的是为了确保 Class 文件中的字节流包含的信息符合当前虚拟机的要求，而且不会危害虚拟机自身的安全。

包括以下四个阶段的验证：

1. 文件格式的验证

   是否以 `0xCAFEBABE` 开头、主次版本号是否在当前虚拟机的处理范围之内、常量池中的常量是否有不被支持的类型。

2. 元数据的验证

   对字节码描述的信息进行语义分析。例如：这个类是否有父类，除了java.lang.Object 之外。

3. 字节码验证

   通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的。

4. 符号引用验证

###### 准备

为类的<span style="color: red">静态变量</span>分配内存，并根据数据类型将其初始化为默认的<span style="color: red">零值</span>(注意不是程序中设置的值)

<span style="color: red">注意</span>：如果是常量(static final)，且在声明时就指定了初始值，则会直接赋值为指定值。

###### 解析

将常量池中的符号引用转换为直接引用。

###### 初始化

初始化**类变量**

**类初始化时机**：

1. 创建类的实例，也就是 new 一个对象
2. 访问某个类或接口的静态变量
3. 调用类的静态方法
4. 反射 `Class.forName("")`
5. 初始化一个类的子类（会首先初始化子类的父类）
6. JVM 启动时标明的启动类

### 集合框架

### IO

### 多线程

## 设计模式

### [单例模式](https://libo9527.github.io/2020/10/22/Design-Pattern-Singleton/)

### 工厂模式

### 责任链模式

### 包装模式

### 策略模式

### 模版方法模式

### 代理模式

## 数据结构

### B+ 树

### 红黑树

## 网络

### TCP

### HTTP

### HTTPS

## 数据库

### Mysql

### Oracle

### MongoDB

## Linux

## 算法

### 排序算法

### 动态规划

### 推荐算法

## 缓存

### Redis

## 消息中间件

### RebbitMQ

### Kafka

## 容器

### Docker

## 操作系统

## 架构

### SSH

### SSM

### 微服务

### 分布式

### 集群