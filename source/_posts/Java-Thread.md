---
title: Java Thread
description: Java 多线程
comments: false
hidden: false
top: false
date: 2020-10-26 10:56:51
categories:
tags:
---

<img src="cover" width="100%"/>

<!-- more -->

## 创建线程的方式

1. 实现 Runnable 接口

   继承 Thread 类，覆盖 run 方法，调用 `Thread#start` 启动

2. 继承 Thread 类

   实现 Runnable 接口，创建实例，作为 `Thread` 构造参数传入，调用 `Thread#start` 启动

3. 实现 Callable 接口

   实现 Callable 接口，创建实例，作为 `FutureTask<>` 构造参数创建 `FutureTask` 对象，将 FutureTask 对象作为 `Thread` 构造参数传入，调用 `Thread#start` 启动

4. 线程池

   使用 ThreadPoolExecutor 创建一个线程池，利用 `submit()` 方法提交线程，`Future<Object>` 接收返回

#### 区别

##### 继承 Thread 和实现 Runnable、Callable 接口区别

实现接口不影响继承其他类

##### 实现 Runnable 和 Callable的区别

Runnable 无返回，Callable 有返回。

##### Thread 启动和线程池启动方式

线程池能够重复使用线程，节省 CPU 资源。

