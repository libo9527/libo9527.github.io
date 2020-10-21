---
title: Study Notes of Nginx
description: Nginx 学习笔记
comments: false
hidden: false
top: false
date: 2020-10-21 10:50:33
categories:
- Study Notes
- Nginx
tags: Nginx
---

<img src="https://www.nginx.com/wp-content/uploads/2018/08/NGINX-logo-rgb-large.png" width="100%"/>

<!-- more -->

## 简介

> Nginx是异步框架的网页服务器，也可以用作反向代理、负载平衡器和HTTP缓存。该软件由伊戈尔·赛索耶夫创建并于2004年首次公开发布。2011年成立同名公司以提供支持。2019年3月11日，Nginx公司被F5 Networks以6.7亿美元收购。 Nginx是免费的开源软件，根据类BSD许可证的条款发布。 [维基百科](https://zh.wikipedia.org/zh-cn/Nginx)

Nginx 是一款轻量级的 WEB 服务器，采用事件驱动的异步非阻塞处理方式，具有极好的 IO 性能，时常用于**反向代理**、**负载均衡**和HTTP缓存。

## Q & A

### 为什么说 Nginx 是轻量级的 WEB 服务器？

> 什么是轻量级的 WEB 服务器?

由于 Nginx 采用 Reactor 模型，使得使用一个进程就可以处理多个请求，而重量级服务器对于每个请求都需要一个进程处理，进程会占用资源，当并发量大的时候，会导致很高的内存占用和CPU占用，也就是所谓的“重量级”。

也就是说，轻量级与重量级是指占用机器资源的大小，占用资源多的叫重量级，占用少的叫轻量级。

