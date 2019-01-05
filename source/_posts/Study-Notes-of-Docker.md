---
title: Study Notes of Docker
description: Docker 学习笔记
hidden: true
date: 2019-01-05 15:23:38
categories:
 - Study Notes
tags:
 - Docker
---

<img src="http://finworks.com/wp-content/uploads/2017/01/Docker_Logo-500x250.png" width="100%"/>

<!-- more -->

> [Docker —— 从入门到实践](https://legacy.gitbook.com/book/yeasy/docker_practice)

## 简介

Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。**它是目前最流行的 Linux 容器解决方案。

Docker基于Go语言开发，是DotCloud开源的、可以将任何应用包装在Linux container中运行的工具。

### 基本概念

#### 镜像

​	Docker 镜像是一个**特殊的文件系统**，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

#### 容器

​	镜像（`Image`）和容器（`Container`）的关系，就像是面向对象程序设计中的 `类` 和 `实例` 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

​	容器的实质是进程。

## 命令

**启动 Docker**

> [Control Docker with systemd](https://docs.docker.com/config/daemon/systemd/)

```shell
systemctl start docker
```

**列出本地镜像**

```shell
docker images
```

**启动容器**

> [Docker run 命令](http://www.runoob.com/docker/docker-run-command.html)

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

- **-d:** 后台运行容器，并返回容器ID；
- **-p:** 端口映射，格式为：主机(宿主)端口:容器端口
- **--name="nginx-lb":** 为容器指定一个名称；
- **-e username="ritchie":** 设置环境变量；

**查看容器**

> [Docker ps 命令](http://www.runoob.com/docker/docker-ps-command.html)

```shell
docker ps [OPTIONS]
```

- **-a :**显示所有的容器，包括未运行的。