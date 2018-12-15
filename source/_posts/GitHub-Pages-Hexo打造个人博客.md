---
title: GitHub Pages+Hexo打造个人博客
date: 2018-12-15 14:34:32
tags:
---

# GitHub Pages+Hexo打造个人博客

> 环境：
>
> Windows 10，x64
>
> node.js v10.14.2
>
> npm 6.5.0
>
>

## GitHub创建Github Pages项目

创建一个名称为`username.github.io`的新仓库。username为你的GitHub账号用户名。

## 搭建Hexo环境

### 安装node.js

#### 简介

> [Node.js是用来做什么的？ - 知乎](https://www.zhihu.com/question/33578075)

**Node.js**是一个 JavaScript 运行环境。

#### 下载与安装

去[官网](https://nodejs.org/zh-cn/)下载对应版本

下载完后双击傻瓜式安装即可

安装完后**win+R** 输入**cmd**打开终端，然后输入`node -v`即可查看Node版本

### 安装npm

#### 简介

npm是Node.js的包管理工具

> [npm安装 - 安装Node.js和npm - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/00143450141843488beddae2a1044cab5acb5125baf0882000)
>
> 为啥我们需要一个包管理工具呢？因为我们在Node.js上开发时，会用到很多别人写的JavaScript代码。如果我们要使用别人写的某个包，每次都根据名称搜索一下官方网站，下载代码，解压，再使用，非常繁琐。于是一个集中管理的工具应运而生：大家都把自己开发的模块打包后放到npm官网上，如果要使用，直接通过npm安装就可以直接用，不用管代码存在哪，应该从哪下载。
>
> 更重要的是，如果我们要使用模块A，而模块A又依赖于模块B，模块B又依赖于模块X和模块Y，npm可以根据依赖关系，把所有依赖的包都下载下来并管理起来。否则，靠我们自己手动管理，肯定既麻烦又容易出错。

#### 安装

安装完Node.js以后npm已经顺带被安装了。

命令行输入`npm -v`即可查看npm版本信息。

### 安装Hexo

```SHELL
npm install -g hexo
```

#### 初始化

新建一个空文件夹，在该目录下右键选择`Git Bash Here`（需要安装[Git客户端](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)）

执行命令（需要注意的是该命令必须在空目录下执行）：

```shell
hexo init
```

安装依赖：

```shell
npm install
```

## 利用Travis-CI自动发布博客

> hexo生成的静态文件统一存放在public目录下，其余的文件都是hexo用来生成静态网页的。
>
> 为了能够适应不同环境（不同主机、不同系统、甚至在GitHub网页端操作等），可以利用Travis-CI对hexo进行[持续集成](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html) 。

### 远程仓库创建hexo分支对hexo源码进行版本控制

1. 将远程仓库clone到本地

   ```shell
   git clone https://github.com/username/username.github.io.git
   ```

2. 创建本地分支hexo并切换到该分支

   ```shell
   git checkout -b hexo
   ```

3. 将本地仓库里的文件清空（除了`.git`文件），替换为hexo源码文件（即`hexo init`生成的那些文件）。

4. 添加并提交到本地仓库

   ```shell
   git add .
   git commit -m "提交信息"
   ```

5. 创建并提交到远程hexo分支

   ```shell
   git push --set-upstream origin hexo　
   ```

### GitHub生成Access Token

头像>Settings>Developer settings>Personal access tokens

点击Generate new token生成一个token

![](https://i.loli.net/2018/12/15/5c14c217c5fb9.jpg)

注意：token生成后只又一次查看的机会，一定要保存好，否则要删掉重新生成

### 设置Travis-CI

使用GitHub账号登录[Travis-CI](https://travis-ci.org/)

![](https://i.loli.net/2018/12/15/5c14c08f2392e.jpg)

点击Settings，将GitHub生成的token添加到里面

![](https://i.loli.net/2018/12/15/5c14c305516b7.jpg)

### hexo源码仓库中添加Travis-CI的配置文件`.travis.yml`

```yml
language: node_js  #设置语言

node_js: stable  #设置相应的版本

# 开始构建
before_install:
  - export TZ='Asia/Shanghai'  #统一构建环境和博客配置的时区, 防止文章时间错误
  
install:
  - npm install  #配置Hexo环境

script:
  - hexo cl  #清除
  - hexo g  #生成

after_script:
  - git clone https://${GH_REF} .deploy_git
  - cd .deploy_git
  - git checkout master
  - cd ../
  - mv .deploy_git/.git/ ./public/
  - cd ./public
  - git config user.name "username"  #github用户名
  - git config user.email "email@xxx.com"  #邮箱
  - git add .
  - git commit -m "Travis CI Auto Builder at `date +"%Y-%m-%d %H:%M"`"  #提交时的说明
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master  #GH_TOKEN是在Travis中配置Token的名称
# 结束构建

branches:
  only:
    - hexo  #只监测master之外新增的hexo分支
env:
 global:
   - GH_REF: github.com/username/username.github.io.git  #设置GH_REF

cache:
  directories:  #缓存特定目录, 加快构建速度
    - node_modules
```

## 填坑记录

### 选用主题后页面空白

原因：themes目录下主题相关文件未正确提交到远程仓库

解决：1. 删除主题目录下的`.git`目录和`.gitignore`文件

     2. ```shell
        git rm --cached 主题目录
        git add 主题目录
        git push
        ```
