---
title: Configing Procy of Network in Mac Terminal
description: Mac 终端中配置网络代理
date: 2020-03-03 14:24:11
categories:
tags:
---

<img src="https://i.ytimg.com/vi/dMLK0cx7V_Y/maxresdefault.jpg" width="100%"/>

<!-- more -->

{% note %}

在用 IDEA 调用 Google Calendar API 的时候，报 `Connect time out ` 的错，但电脑已经打开了 shadowsocks，浏览器也可以正常访问 Google。搜索后发现是终端中的代理需要另外设置。

浏览器的翻墙通过 [shadowsocks](https://github.com/shadowsocks/ShadowsocksX-NG) 实现，但是终端中还是会走默认的网关。

{% endnote %}

## 查看当前公网网关

```shell
$ curl ip.cn
# or
$ curl cip.cc
```

## 当前窗口中配置

打开一个终端窗口，输入一下命令：

```shell
$ export http_proxy=http://ip:port
$ export https_proxy=http://ip:port
```

ip 和 port 可以从 `shadowsocks>HTTP代理设置` 中查看。

这种方式只对当前窗口有效，关闭后失效。每次打开新的窗口时需要重新输入配置命令。

## 将配置写入用户脚本文件中

1. 在 `~/.bash_profile` 中添加以下代码：

   ```bash
   function proxy_on(){
       export http_proxy=http://127.0.0.1:1087
       export https_proxy=http://127.0.0.1:1087
       echo -e "已开启代理"
   }
   function proxy_off(){
       unset http_proxy
       unset https_proxy
       echo -e "已关闭代理"
   }
   ```

2. 重新执行刚修改的脚本文件，使之立即生效

   ```bash
   $ source ~/.bash_profile
   ```

3. 开启代理

   ```shell
   $ proxy_on
   ```

4. 关闭代理

   ```shell
   $ proxy_off
   ```

   

## IDEA 配置代理

`Preferences>Appearance & Behavior>System Settings>HTTP Proxy>Manual proxy configurationation`

填写相应的 Host 和 Port 即可。

![](https://img2018.cnblogs.com/blog/478956/201810/478956-20181019112723115-194000758.png)

