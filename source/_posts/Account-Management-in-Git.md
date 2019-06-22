---
title: {{ Account Management in Git }}
description: Git 中的账号管理
date: {{ date }}
categories: Git
tags: Git

---

<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR47zQflbjfccDWmMhgzZ6Na_e2jNfmWLhWHYSmZbjcySYzqnVTbw" width="100%"/>

<!-- more -->

## Git 中 GitHub 账号的管理

本文记录关于 Git 中管理账号时可能遇到的一些问题，使用的远程仓库托管平台是 GitHub。

### 单账号管理

> 假设 GitHub 的账号信息如下：
>
> 账号：username
>
> 密码：password
>
> 假设 GitHub 中托管的仓库地址如下：
>
> HTTPS：`https://github.com/username/repository.git`
>
> SSH：`git@github.com:username/repository.git`

Git 中链接远程仓库有 HTTPS 和 SSH 两种方式，HTTPS 相对比较简单，而 SSH 则具有更高的安全性。

#### HTTPS 方式

使用 HTTPS 的方式时，会遇到每次提交都要输入 GitHub 的账号密码的问题，可以通过将账号密码缓存到内存或者保存到本地磁盘来解决这个问题。具体详情可查看【[凭证存储 - Git](https://git-scm.com/book/zh/v2/Git-工具-凭证存储)】

```shell
# 将账号密码保存到磁盘，默认存储位置是 ~/.git-credentials
$ git config --global credential.helper store
```

但这种方式是明文存储的，查看 `~/.git-credentials` 可以看到具体的账号信息。

```
https://username:password@github.com
```

#### SSH 方式

通过 SSH 方式链接 GitHub 时需要通过密钥来实现，所以 SSH 具有比较高的安全性。

1. 生成密钥对

   ```shell
   # -t 指定密钥类型，默认是 rsa
   # -C 注释
   # -f 指定文件名，默认是 ~/.ssh/id_rsa（私钥）和 ~/.ssh/id_rsa.pub（公钥）
   $ ssh-keygen -t rsa -C "username@email.com"
   ```

   生成密钥时会提示让输入密码，此时的密码是以后 Git 提交时需要输入的密码，可以不填直接回车，那在提交的时候就不需要再输入密码了。

2. GitHub 中添加公钥

   登陆 GitHub，在 `Personal settings > SSH and GPG keys > New SSH key` 将 `~/.ssh/id_rsa.pub` 文件中公钥添加进去。

3. 设置本地仓库中的远程仓库地址为 SSH 格式

   ```shell
   $ git remote set-url origin "git@github.com:username/repository.git"
   # 或者
   $ git config remote.origin.url "git@github.com:username/repository.git"
   ```

### 多账号管理

> 假设 GitHub 的账号信息如下：
>
> 账号A
>
> - 账号：usernameA
>- 密码：passwordA
> - 邮箱：usernameA@163.com
>
> 账号B
>
> - 账号：usernameB
>- 密码：passwordB
> - 邮箱：usernameB@163.com
>
> 假设 GitHub 中托管的仓库地址如下：
>
> 仓库A
>
> - HTTPS：`https://github.com/usernameA/repositoryA.git`
>
> - SSH：`git@github.com:usernameA/repositoryA.git`
>
> 仓库B
>
> - HTTPS：`https://github.com/usernameB/repositoryB.git`
>
> - SSH：`git@github.com:usernameB/repositoryB.git`

#### 账号切换

> 假设账号A和账号B都有权限管理仓库A，此时想将本地仓库原本的账号A切换到账号B。

1. 查看本机是否缓存或保存了账号A的账号信息

   ```shell
   # 查看 local 级别的配置中是否有关于 credential.helper 的配置
   $ git config --local --get credential.helper
   # 查看 global 级别的配置中是否有关于 credential.helper 的配置
   $ git config --global --get credential.helper
   # 查看 system 级别的配置中是否有关于 credential.helper 的配置
   $ git config --system --get credential.helper
   ```

   如果返回结果为：`cache` /  `store` /  `osxkeychain` /  `winstore` 则表示本地存在账号A的账号信息

2. 如果存在账号A的信息则清除掉其账号信息

   删除 `~/.git-credentials` 文件中账号A的账号信息

3. 切换本地仓库中的 Git 用户名和邮箱

   ```shell
   $ git config --local user.name "usernameB"
   $ git config --local user.email "usernameB@163.com"
   ```

4. 将账号B的账号信息保存到本机

   ```shell
   $ git config --local credential.helper store
   ```

   下一次提交时会提示让输入用户名和密码，输入账号B的用户名和密码即可。

经过以上 4 步之后就完成了账号的切换工作，之后再提交时使用的就是账号B了。

#### 多账号共存

以上情况都是本机中只存在一个 GitHub 账号，如果需要使用不同的账号管理不同的仓库时则需要对每个仓库和账号进行相应的配置以达到理想的匹配作用。

##### HTTPS 方式

如果想要本机中同时保存账号A和账号B的账号信息，对于本地仓库A，可能随时想要选用账号A或账号B中的一个来提交时。只需要在本地仓库的远程仓库地址中加上相应的用户名，然后再将 Git 用户名和密码切换到相应的用户即可。

```shell
# 先设置保存密码的配置
$ git config --local credential.helper store
# 想要切换到账号A时
$ git remote set-url origin "https://usernameA@github.com/usernameA/repositoryA.git"
$ git config --local user.name "usernameA"
$ git config --local user.email "usernameA@163.com"
# 想要切换到账号B时
$ git remote set-url origin "https://usernameB@github.com/usernameA/repositoryA.git"
$ git config --local user.name "usernameB"
$ git config --local user.email "usernameB@163.com"
```

##### SSH 方式

> 假设要通过 SSH 的方式来让账户A和账户B再本地仓库A中自由的切换

使用 SSH 方式时需要对不用的账号生成不同的密钥，并配置账号与 GitHub 域名的匹配信息。

1. 生成各自密钥

   ```shell
   # -t 指定密钥类型，默认是 rsa
   # -C 注释
   # -f 指定文件名，默认是 ~/.ssh/id_rsa（私钥）和 ~/.ssh/id_rsa.pub（公钥）
   $ ssh-keygen -t rsa -C "usernameA@email.com" -f "id_rsa_A"
   $ ssh-keygen -t rsa -C "usernameB@email.com" -f "id_rsa_B"
   ```

2. 将密钥全部添加到 SSH Agent 中

   > 部分终端中需要手动开启 ssh-agent
   >
   > ```shell
   > $ ssh-agent bash
   > ```

   ```shell
   $ ssh-add ~/.ssh/id_rsa_A
   $ ssh-add ~/.ssh/id_rsa_B
   ```

3. 添加配置文件 `~/.ssh/config`

   ```shell
   # usernameA
   Host hostA #别名，之后配置本地仓库的远程仓库地址时需要使用
     HostName github.com
     IdentityFile ~/.ssh/id_rsa
     User usernameA
   # usernameB
   Host hostB
     HostName github.com
     IdentityFile ~/.ssh/id_rea2
     User usernameB
   ```

4. 远程仓库托管平台中添加对应的公钥

   登陆相应的远程仓库托管平台，将公钥添加上去。

5. 修改本地仓库对应的远程仓库地址

   ```shell
   # 想要切换到账户A时
   $ git remote set-url origin "git@hostA:usernameA/repositoryA.git"
   $ git config --local user.name "usernameA"
   $ git config --local user.email "usernameA@163.com"
   # 想要切换到账号B时
   $ git remote set-url origin "git@hostB:usernameA/repositoryA.git"
   $ git config --local user.name "usernameB"
   $ git config --local user.email "usernameB@163.com"
   ```

   