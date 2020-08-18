---
title: Config ssh for Tencent Server login
description: SSH 免密登陆
comments: false
hidden: false
top: false
date: 2019-08-17 20:01:50
categories:
tags:
---

<img src="https://blog.yangerxiao.com/images/ssh.png" width="100%"/>

<!-- more -->

## SSH 连接服务器

### 使用密码登陆

`ssh username@ip`

输入密码后回车

### 使用密钥登陆

1. 生成密钥

   ```bash
   # -t 指定加密算法；
   # -b 指定生成的密钥长度；
   # -C 一段字符，一般都填邮箱地址。
   # 更多参数说明可以在终端输入：ssh-keygen --help 查看
   ssh-keygen -t rsa -b 4096 -C "你的邮箱地址"
   ```

   这条命令执行完之后，会提示你指定公钥和私钥的存储位置。默认文件名是`id_rsa`和`id_rsa.pub`，可以通过`-f`指定文件名

2. 将共钥上传到远程主机

   ```bash
   ssh-copy-id -i id_rsa.pub username@ip
   ```

3. 配置别名

   在`~/.ssh/config`文件(没有就创建一个)中添加以下内容

   ```
   Home nickname
   	HostName ip
   	User username
   	Port 22
   	IdentityFile ~/.ssh/id_rsa
   ```

至此，就可以使用`ssh nickname`直接免密登陆服务器了

### Q & A

#### 远程主机标识已更改

```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:Ym+3hql3pLFI1Lkxbpym4xQ7RvHTTQWh+ZCxHJR247Q.
Please contact your system administrator.
Add correct host key in /c/Users/Administrator/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /c/Users/Administrator/.ssh/known_hosts:3
ECDSA host key for 39.108.83.12 has changed and you have requested strict checking.
Host key verification failed.
```

原因：以前使用别的密钥登陆过，需要在`~/.ssh/known_hosts`文件中更新相关信息

方法：使用命令`ssh-keygen -R ip`即可删除`known_hosts`文件中该 IP 相关的旧纪录



