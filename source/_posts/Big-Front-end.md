---
title: Big Front-end
description: 大前端
comments: false
hidden: false
top: false
date: 2020-08-18 11:52:22
categories:
tags:
---

<img src="cover" width="100%"/>

<!-- more -->

## Linux

### 查看Linux内核版本信息

1. 使用`lab_reldase -a`查看内核版本信息

   > **Linux标准规范**（英语：Linux Standard Base，缩写为 LSB）是一个在[Linux基金会](https://zh.wikipedia.org/wiki/Linux基金會)结构下对[Linux发行版](https://zh.wikipedia.org/wiki/Linux發行版)的联合项目，其目标使[Linux](https://zh.wikipedia.org/wiki/Linux)[操作系统](https://zh.wikipedia.org/wiki/作業系統)符合软件系统架构，或文件系统架构标准的规范及标准。

   Centos 7下找不到`lab_release`命令，使用`yum install redhat-lsb -y`安装，注意Centos下的`lib_release`命令的安装包的名字是`redhat-lsb`。

   ```bash
   [root@VM-0-10-centos ~]# lsb_release
   LSB Version:	:core-4.1-amd64:core-4.1-noarch:cxx-4.1-amd64:cxx-4.1-noarch:desktop-4.1-amd64:desktop-4.1-noarch:languages-4.1-amd64:languages-4.1-noarch:printing-4.1-amd64:printing-4.1-noarch
   ```

   ```bash
   [root@VM-0-10-centos ~]# lsb_release -a
   LSB Version:	:core-4.1-amd64:core-4.1-noarch:cxx-4.1-amd64:cxx-4.1-noarch:desktop-4.1-amd64:desktop-4.1-noarch:languages-4.1-amd64:languages-4.1-noarch:printing-4.1-amd64:printing-4.1-noarch
   Distributor ID:	CentOS
   Description:	CentOS Linux release 7.5.1804 (Core) 
   Release:	7.5.1804
   Codename:	Core
   ```

2. `unman -a`查看Linux内核版本

   ```bash
   [root@VM-0-10-centos ~]# uname -a
   Linux VM-0-10-centos 3.10.0-862.el7.x86_64 #1 SMP Fri Apr 20 16:44:24 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
   ```

### 查看磁盘占用情况

```bash
[root@VM-0-10-centos ~]# df -Th
文件系统       类型      容量  已用  可用 已用% 挂载点
/dev/vda1      ext4       50G  1.9G   45G    4% /
devtmpfs       devtmpfs  909M     0  909M    0% /dev
tmpfs          tmpfs     920M   24K  920M    1% /dev/shm
tmpfs          tmpfs     920M  500K  919M    1% /run
tmpfs          tmpfs     920M     0  920M    0% /sys/fs/cgroup
tmpfs          tmpfs     184M     0  184M    0% /run/user/0
```

-T, --print-type   print file system type

-h, --human-readable print sizes in human readable format (e.g., 1K 234M 2G)

### Linux 目录结构

> [Linux 系统目录结构| 菜鸟教程](https://www.runoob.com/linux/linux-system-contents.html)

```bash
[root@VM-0-10-centos /]# ls -la
总用量 80
dr-xr-xr-x. 19 root root  4096 8月  18 12:14 .
dr-xr-xr-x. 19 root root  4096 8月  18 12:14 ..
lrwxrwxrwx.  1 root root     7 8月   8 2018 bin -> usr/bin
drwxr-xr-x  19 root root  2980 8月  17 19:35 dev
drwxr-xr-x. 93 root root 12288 8月  18 12:07 etc
drwxr-xr-x.  2 root root  4096 4月  11 2018 home
lrwxrwxrwx.  1 root root     7 8月   8 2018 lib -> usr/lib
lrwxrwxrwx.  1 root root     8 8月   8 2018 sbin -> usr/sbin
dr-xr-xr-x  13 root root     0 8月  17 19:34 sys
drwxrwxrwt.  8 root root  4096 8月  18 12:07 tmp
drwxr-xr-x. 13 root root  4096 8月   8 2018 usr
drwxr-xr-x. 19 root root  4096 1月  21 2019 var
```

第一列是文件/目录及权限信息，前面的`d`代表目录，后面九位是权限信息， `r`代表读，`w`代表写，`x`代表可执行，分为三组，第一组是用户权限，第二组是组权限，第三组是其他用户权限。

#### 目录结构

/home：用户目录

/etc：配置目录

/sys：系统目录

/usr：存放用户应用程序

/usr/sbin：存放超级用户使用的比较高级的管理程序和系统守护程序

/var：这个目录中存放着在不断扩充着的东西，习惯将那些经常被修改的目录放在这个目录下，包括各种日志文件

### CPU/内存/进程

#### top

```bash
[root@VM-0-10-centos ~]# top

top - 12:27:26 up 16:52,  2 users,  load average: 0.01, 0.02, 0.05
Tasks:  73 total,   1 running,  72 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1882752 total,   724016 free,    99704 used,  1059032 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  1599800 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND     
  538 root      20   0  588564  11116   3240 S  0.3  0.6   1:26.72 YDService   
 1418 root      20   0  611520  13664   2364 S  0.3  0.7   3:21.65 barad_agent 
    1 root      20   0   43424   3712   2536 S  0.0  0.2   0:04.43 systemd     
```

`Tasks:  73 total,   1 running,  72 sleeping,   0 stopped,   0 zombie`

总共73个进程

### 文件

#### 编辑后不保存

`:q!`

#### 追加内容

`echo 'xxxxx' >> filename`

#### 覆盖

`echo 'xxxx' > filename`

#### 下载

`wget url`

#### 解压缩

解压`apache-tomcat-8.5.57.tar.gz`

```bash
tar zxvf apache-tomcat-8.5.57.tar.gz
```

-z, --gzip, --gunzip, --ungzip  通过 gzip 过滤归档（`.gz`结尾）

-x, --extract, --get    从归档中解出文件

-f, --file=ARCHIVE     使用归档文件或 ARCHIVE 设备

​     --force-local

-v, --verbose       详细地列出处理的文件

​     --warning=KEYWORD   警告控制:

压缩

```bash
tar zcvf apache-tomcat-8.5.57
```

-c, --create        创建一个新归档

#### grep

查看名字中包含`docker`的进程

```bash
ps -ef | grep docker
```

#### kill

终止进程

```bash
kill -9 PID
```

#### 查看服务运行状态

```bash
service sshd status
```

### 关闭服务

```bash
service sshd stop
```

### 重启服务

```bash
service sshd restart
```

#### systemctl

systemctl 命令是系统服务管理器指令

```bash
systemctl status firewalld.service
```

如何改ssh端口？

#### netstat

```bash
netstat -anlp | grep sshd
```

#### semanage

```bash
semanage port -a -t ssh_port_t -p tcp 10022
```

```bash
semanage port -l | grep ssh
```



#### yum

```bash
yum whatprovides semanage
```

```bash
yum install -y policycoreutils-python
```



```bash
sudo yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docekr-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine
```

```bash
sudo yum install -y yum-utils \
device-mapper-persistent-data \
lvm2
```

### Centos 7 修改 SSH 端口

1. 修改ssh配置文件
2. 重启ssh服务
3. 

## Docker

> www.toimc.com

#### Docker VS 虚拟机

#### Docker主要特性

对文件、资源、网络进行隔离

变更管理、日志记录

写时复制

Docker Compose

Docker hub、Harbor



## Node

什么是nodejs？

- 免费、开源、跨平台

- 服务器上的javascript运行环境

- 基于Chrome V8引擎，事件驱动，非阻塞式I/O

  事件驱动：餐厅举手叫服务员(事件)，服务员(函数)响应

  非阻塞式I/O：排队叫号

### 包管理工具

npm、cnpm

yarn

### node版本管理工具

nvm、nvm-windows

如何发布一个自己的node模块

`npm add user`

`yarn add xxx`等同于`nom install xxx -S`

