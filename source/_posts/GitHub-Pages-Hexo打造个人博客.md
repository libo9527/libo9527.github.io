---
title: GitHub Pages+Hexo打造个人博客
date: 2018-12-15 14:34:32
tags:
password: Mike
abstract: Welcome to my blog, enter password to read.
message: Welcome to my blog, enter password to read.
---


> 环境：
>
> Windows 10，x64
>
> node.js v10.14.2
>
> npm 6.5.0
>
>

<!-- more -->

# GitHub创建Github Pages项目

创建一个名称为`username.github.io`的新仓库。username为你的GitHub账号用户名。

# 搭建Hexo环境

## 安装node.js

### 简介

> [Node.js是用来做什么的？ - 知乎](https://www.zhihu.com/question/33578075)

**Node.js**是一个 JavaScript 运行环境。

### 下载与安装

去[官网](https://nodejs.org/zh-cn/)下载对应版本

下载完后双击傻瓜式安装即可

安装完后**win+R** 输入**cmd**打开终端，然后输入`node -v`即可查看Node版本

## 安装npm

### 简介

npm是Node.js的包管理工具

> [npm安装 - 安装Node.js和npm - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/00143450141843488beddae2a1044cab5acb5125baf0882000)
>
> 为啥我们需要一个包管理工具呢？因为我们在Node.js上开发时，会用到很多别人写的JavaScript代码。如果我们要使用别人写的某个包，每次都根据名称搜索一下官方网站，下载代码，解压，再使用，非常繁琐。于是一个集中管理的工具应运而生：大家都把自己开发的模块打包后放到npm官网上，如果要使用，直接通过npm安装就可以直接用，不用管代码存在哪，应该从哪下载。
>
> 更重要的是，如果我们要使用模块A，而模块A又依赖于模块B，模块B又依赖于模块X和模块Y，npm可以根据依赖关系，把所有依赖的包都下载下来并管理起来。否则，靠我们自己手动管理，肯定既麻烦又容易出错。

### 安装

安装完Node.js以后npm已经顺带被安装了。

命令行输入`npm -v`即可查看npm版本信息。

## 安装Hexo

```SHELL
npm install -g hexo
```

### 初始化

新建一个空文件夹，在该目录下右键选择`Git Bash Here`（需要安装[Git客户端](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)）

执行命令（需要注意的是该命令必须在空目录下执行）：

```shell
hexo init
```

安装依赖：

```shell
npm install
```

# 利用Travis-CI自动发布博客

> hexo生成的静态文件统一存放在public目录下，其余的文件都是hexo用来生成静态网页的。
>
> 为了能够适应不同环境（不同主机、不同系统、甚至在GitHub网页端操作等），可以利用Travis-CI对hexo进行[持续集成](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html) 。

## 远程仓库创建hexo分支对hexo源码进行版本控制

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

## GitHub生成Access Token

头像>Settings>Developer settings>Personal access tokens

点击Generate new token生成一个token

![](https://i.loli.net/2018/12/15/5c14c217c5fb9.jpg)

注意：token生成后只又一次查看的机会，一定要保存好，否则要删掉重新生成

## 设置Travis-CI

使用GitHub账号登录[Travis-CI](https://travis-ci.org/)

![](https://i.loli.net/2018/12/15/5c14c08f2392e.jpg)

点击Settings，将GitHub生成的token添加到里面

![](https://i.loli.net/2018/12/15/5c14c305516b7.jpg)

## hexo源码仓库中添加Travis-CI的配置文件`.travis.yml`

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

# 主题优化

> [NexT使用文档](http://theme-next.iissnan.com/)

## 文章预览

> [如何设置「阅读全文」？](http://theme-next.iissnan.com/faqs.html#read-more)

## 评论系统

> [Hexo-Next 添加 Gitment 评论系统](https://ryanluoxu.github.io/2017/11/27/Hexo-Next-%E6%B7%BB%E5%8A%A0-Gitment-%E8%AF%84%E8%AE%BA%E7%B3%BB%E7%BB%9F/)
>
> [hexo博客配置-添加评论系统-gitment和valine-需注册](https://xiaotiandi.github.io/publicBlog/2018-09-19-d196c9ad.html)
>
> [为 hexo NexT 添加 Gitment 评论插件](https://meesong.github.io/StaticBlog/2017/NexT+Gitment/)
>
> [在hexo next主题上使用gitalk](https://github.com/gitalk/gitalk/wiki/%E5%9C%A8hexo-next%E4%B8%BB%E9%A2%98%E4%B8%8A%E4%BD%BF%E7%94%A8gitalk)
>
> [为你的Hexo加上评论系统-Valine](https://www.bluelzy.com/articles/use_valine_for_your_blog.html)

![](https://i.loli.net/2018/12/17/5c171d0e2a281.jpg)

![](https://i.loli.net/2018/12/17/5c171f261cf6c.jpg)

由于gitment长期未维护,所有最后使用gitalk,之后可以增加其他三方的评论插件更加灵活.

## 自定义内建标签

> [Hexo next博客添加折叠块功能添加折叠代码块](https://blog.rmiao.top/hexo-fold-block/)

## 进度条

> [**theme-next-pace**](https://github.com/theme-next/theme-next-pace)

## 自定义页面样式

> [2017年最新基于hexo搭建个人免费博客——自定义页面样式一](http://www.cduyzh.com/hexo-settings-3/)
>
> [优化 网页样式布局](https://reuixiy.github.io/technology/computer/computer-aided-art/2017/06/09/hexo-next-optimization.html#%E4%BC%98%E5%8C%96-%E7%BD%91%E9%A1%B5%E6%A0%B7%E5%BC%8F%E5%B8%83%E5%B1%80)

## 显示文章更新时间

> [~~hexo添加文章更新时间~~](https://blog.csdn.net/ganzhilin520/article/details/79053399)

修改主题配置文件的

```yaml
post_meta:
  updated_at: 
    enabled: true
```

要想具体显示到时分秒,则修改站点配置文件的

```yaml
date_format: YYYY-MM-DD HH:mm:ss
```

## 复制代码按钮

> [~~HEXO优化之（二）----添加复制功能~~](https://www.ofind.cn/blog/HEXO/HEXO%E4%BC%98%E5%8C%96%E4%B9%8B%EF%BC%88%E4%BA%8C%EF%BC%89-%E6%B7%BB%E5%8A%A0%E5%A4%8D%E5%88%B6%E5%8A%9F%E8%83%BD.html)
>
> [Hexo NexT主题代码块添加复制功能](http://www.missfli.com/2018/06/19/github-hexo-next-08.html)



## 显示当前浏览进度

> [7.浏览页面的时候显示当前浏览进度](https://www.jianshu.com/p/3ff20be8574c)

## 背景图片

> [添加背景图](https://www.simon96.online/2018/10/12/hexo-tutorial/#%E6%B7%BB%E5%8A%A0%E8%83%8C%E6%99%AF%E5%9B%BE)

## 文章加密

> [hexo-blog-encrypt](https://github.com/MikeCoder/hexo-blog-encrypt/blob/master/ReadMe.zh.md)

注意实在站点配置文件中添加

```yaml
# Security
##
encrypt:
    enable: true
```

## 二次元看板娘

> [hexo-helper-live2d](https://github.com/EYHN/hexo-helper-live2d/blob/master/README.zh-CN.md)

## 填坑记录

## 选用主题后页面空白

原因：themes目录下主题相关文件未正确提交到远程仓库

解决：1. 删除主题目录下的`.git`目录和`.gitignore`文件

     2. ```shell
        git rm --cached 主题目录
        git add 主题目录
        git push
        ```
