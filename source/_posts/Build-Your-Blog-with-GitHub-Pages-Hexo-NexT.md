---
title: Build Your Blog with GitHub Pages+Hexo+NexT
date: 2018-12-15 14:34:32
top: true
hidden: false
categories:
 - Blog
tags:
 - GitHub Pages
 - Hexo
 - NexT
---

![NexT](https://i.loli.net/2018/12/26/5c231d3ac8a4c.jpg)

<!-- more -->

> 环境：
>
> Windows 10，x64
>
> node.js v10.14.2
>
> npm 6.5.0

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

## 开始构建
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
## 结束构建

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

> 原理图
>
> ![](https://i.loli.net/2018/12/25/5c219ecc93bcd.png)

## 主题优化

> [NexT使用文档](http://theme-next.iissnan.com/)

### 文章预览

> [如何设置「阅读全文」？](http://theme-next.iissnan.com/faqs.html#read-more)

#### 文章封面

1. 关闭**主体配置文件**中的`Automatically Excerpt`

   ```yaml
   # Automatically excerpt description in homepage as preamble text.
   excerpt_description: false
   
   # Automatically Excerpt. Not recommend.
   # Please use <!-- more --> in the post to control excerpt accurately.
   auto_excerpt:
     enable: false
     length: 150
   ```

2. 在`手动截断标签`上只放一张图片即可

   ```html
   ![NexT](https://i.loli.net/2018/12/26/5c231d3ac8a4c.jpg)
   <!-- more -->
   ```

### 评论系统

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

如需取消某个 页面/文章 的评论，在 md 文件的 [front-matter ](https://hexo.io/docs/front-matter.html)中增加 `comments: false`

### 自定义内建标签

> [Hexo next博客添加折叠块功能添加折叠代码块](https://blog.rmiao.top/hexo-fold-block/)

### 进度条

> [**theme-next-pace**](https://github.com/theme-next/theme-next-pace)

### 自定义页面样式

> [2017年最新基于hexo搭建个人免费博客——自定义页面样式一](http://www.cduyzh.com/hexo-settings-3/)
>
> [优化 网页样式布局](https://reuixiy.github.io/technology/computer/computer-aided-art/2017/06/09/hexo-next-optimization.html#%E4%BC%98%E5%8C%96-%E7%BD%91%E9%A1%B5%E6%A0%B7%E5%BC%8F%E5%B8%83%E5%B1%80)

#### 调整文章元信息区域离文章主体的间距

一般文章都会添加`description`和一张图片作为封面，但元信息离正文太远视野上不舒服，故做如下调整。

![Before](https://i.loli.net/2018/12/26/5c23347219069.jpg)

修改`themes\next\source\css\_common\components\post\post-meta.styl`如下：

```css
// before
.posts-expand .post-meta {
  margin: 3px 0 60px 0;
  ...
}

// after
.posts-expand .post-meta {
  margin: 3px 0 10px 0;
  ...
}
```



### 显示文章更新时间

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

### 复制代码按钮

> [~~HEXO优化之（二）----添加复制功能~~](https://www.ofind.cn/blog/HEXO/HEXO%E4%BC%98%E5%8C%96%E4%B9%8B%EF%BC%88%E4%BA%8C%EF%BC%89-%E6%B7%BB%E5%8A%A0%E5%A4%8D%E5%88%B6%E5%8A%9F%E8%83%BD.html)
>
> [Hexo NexT主题代码块添加复制功能](http://www.missfli.com/2018/06/19/github-hexo-next-08.html)



### 显示当前浏览进度

> [7.浏览页面的时候显示当前浏览进度](https://www.jianshu.com/p/3ff20be8574c)

### 背景图片

> [添加背景图](https://www.simon96.online/2018/10/12/hexo-tutorial/#%E6%B7%BB%E5%8A%A0%E8%83%8C%E6%99%AF%E5%9B%BE)

### 文章加密

> [hexo-blog-encrypt](https://github.com/MikeCoder/hexo-blog-encrypt/blob/master/ReadMe.zh.md)

注意实在**站点配置文件**中添加

```yaml
# Security
##
encrypt:
    enable: true
```

#### 给文章添加密码：

```
---
title: hello world
date: 2016-03-30 21:18:02
tags:
    - fdsfadsfa
    - fdsafsdaf
password: Mike
abstract: Welcome to my blog, enter password to read.
message: Welcome to my blog, enter password to read.
---
```

- password: 是该博客加密使用的密码
- abstract: 是该博客的摘要，会显示在博客的列表页
- message: 这个是博客查看时，密码输入框上面的描述性文字

### 二次元看板娘

> [hexo-helper-live2d](https://github.com/EYHN/hexo-helper-live2d/blob/master/README.zh-CN.md)

### 站内搜索

> [theme-next/**hexo-generator-searchdb**](https://github.com/theme-next/hexo-generator-searchdb)

1. `npm install hexo-generator-searchdb --save`

2. **站点配置文件**中添加如下配置：

   ```yaml
   search:
     path: search.xml
     field: post
     format: html
     limit: 10000
   ```

3. **主题配置文件**中打开如下配置：

   ```yaml
   # Local search
   # Dependencies: https://github.com/theme-next/hexo-generator-searchdb
   local_search:
     enable: true
   ```

### 文章置顶+置顶标签

> [hexo博客优化之文章置顶+置顶标签](https://blog.csdn.net/qwerty200696/article/details/79010629)
>
> [Swig » 文档 » 注释](https://github.mayuxiao.com/swig.zh-CN/docs/index.html#comments)

1. 使用插件[hexo-generator-index-pin-top](https://github.com/netcan/hexo-generator-index-pin-top)

   ```shell
   $ npm uninstall hexo-generator-index --save
   $ npm install hexo-generator-index-pin-top --save
   ```

2. 在需要置顶的文章的`Front-matter`中加上`top: true`

3. `/blog/themes/next/layout/_macro` 目录下的`post.swig`文件，定位到`<div class="post-meta">`标签下，做如下修改：

   ```html
   <div class="post-meta">
             <span class="post-time">
   
               {% set date_diff = date(post.date) != date(post.updated) %}
               {% set time_diff = time(post.date) != time(post.updated) %}
               {% set datetime_diff = date_diff or time_diff %}
   
               {# 置顶标签 #}
               {% if post.top %}
                 <i class="fa fa-thumb-tack"></i>
                 <font color=FFC0CB>置顶</font>
                 <span class="post-meta-divider">|</span>
               {% endif %}
               {# 置顶标签 #}
               ...
       </span>
   </div>
   ```

### 版权声明

> [“知识共享”（CC协议）简单介绍](https://zhuanlan.zhihu.com/p/20641764)

1. 修改**主题配置文件**中如下配置：

	```yaml \source\_data
	# Creative Commons 4.0 International License.
	# https://creativecommons.org/share-your-work/licensing-types-examples
	# Available: by | by-nc | by-nc-nd | by-nc-sa | by-nd | by-sa | zero
	creative_commons:
	  license: by-nc-sa
	  sidebar: false
	  post: true
	```

2. 修改**站点配置文件**中如下配置：

   ```yml _config.yml
   # URL
   ## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
   url: http://username.github.io
   ```

### 首页隐藏指定文章

> [Hexo 设置首页隐藏指定文章](https://blog.csdn.net/m0_37323771/article/details/80672271)

1. 对主题 index.swig 文件做如下修改

   ```javascript Hexo\themes\next\layout
   {% for post in page.posts %}
   	{# 首页隐藏指定文章 #}
   	{% if post.hidden != true %}
           {{ post_template.render(post, true) }}
   	{% endif %}
   	{# 首页隐藏指定文章 #}
   {% endfor %}
   ```

2. 在需要首页隐藏的文章 front-matter 中添加 `hidden: true`

## 填坑记录

### 选用主题后页面空白

原因：themes目录下主题相关文件未正确提交到远程仓库

解决：

1. 删除主题目录下的`.git`目录和`.gitignore`文件

2. `git rm --cached 主题目录`
3. `git add 主题目录`
4. `git push`

## 写作

### 布局

> [布局](https://hexo.io/zh-cn/docs/writing.html)

Hexo 有三种默认布局：`post`、`page` 和 `draft`，它们分别对应不同的路径，而您自定义的其他布局和 `post` 相同，都将储存到 `source/_posts` 文件夹。

| 布局  | 路径           |
| ----- | -------------- |
| post  | source/_posts  |
| page  | source         |
| draft | source/_drafts |

#### 草稿

Hexo 的一种特殊布局：`draft`，这种布局在建立文章时会被保存到 `source/_drafts` 文件夹，您可通过 `publish` 命令将草稿移动到 `source/_posts` 文件夹。

```shell
$ hexo publish [layout] <filename>
```

注意：

1. `hexo new`命令新建文章时会将特殊字符（空格、加号等）转化为`-`

   eg：

   ```shell
    $ hexo new "GitHub Pages+Hexo+NexT"
    INFO  Created: E:\xxx\Hexo\source\_posts\GitHub-Pages-Hexo-NexT.md
   ```
```

2. 在使用`publish`命令发布草稿时需要用**文件名**，而不是文章标题

   eg：

   ```shell
   $ hexo publish post GitHub-Pages-Hexo-NexT # post可以省略，因为layout默认就是post
   INFO  Published: E:\Github\Hexo\source\_posts\GitHub-Pages-Hexo-NexT.md
```

3. 文件名为空时默认发布第一篇草稿

   eg：

   ```shell
   $ hexo publish post .
   # 或者
   $ hexo publish post ""
   ```

### 代码块

[**指定语言，附加说明和网址**](https://hexo.io/zh-cn/docs/tag-plugins.html#%E4%BB%A3%E7%A0%81%E5%9D%97)

1. Hexo 格式

   代码：

   ```js
   {% codeblock lang:java title http://www.baidu.com link test %}
   public static void main(String[] args) {
   	...
   }
   {% endcodeblock %}
   ```

   样式：

   ![](https://i.loli.net/2019/01/04/5c2ec9c02da97.jpg)

2. Markdown 格式

   代码：

   ```markdown
   ​``` java title http://www.baidu.com link test
   public static void main(String[] args) {
   	...
   }
   ​
   ```

   样式：

   ![](https://i.loli.net/2019/01/04/5c2ec9c02da97.jpg)

#### 嵌入 CodePen

<iframe height="265" style="width: 100%;" scrolling="no" title="Tree Sort" src="//codepen.io/libo9527/embed/gVjedo/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/libo9527/pen/gVjedo/'>Tree Sort</a> by libo9527
  (<a href='https://codepen.io/libo9527'>@libo9527</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

在 Codepen 中编辑好之后点击页面底部的 `Embed`，复制 `iframe` 的代码，贴到博客中即可。

### 文件下载

> [hexo添加下载功能](http://yanng.cc/2018/03/18/hexo添加下载功能/)

1. 在`source`目录下，新建`download`目录，和`_posts``About``tags``categories`等目录并列。
2. 将文件或者图片放到该目录下；在写博客时，通过诸如`[点击下载](/download/xx.exe)`这样的链接，直接写入。引用图片，采用`![pic](/download/test.png)`。

### 引用站内文章

> [Hexo引用站内文章](https://www.jibing57.com/2017/10/30/how-to-use-post-link-on-hexo/)

#### 语法：

```
{% post_link slug [title] %}
```

- slug 

  _posts 文件夹下需要引用的文章的 markdown 文件的名字，不带后缀

- title

  链接显示的文字

#### eg：

```
{% post_link Comments 留言板 %}
```

#### 效果：

{% post_link Comments 留言板 %}

## 底层原理

> [next主题的模板引擎swig语法介绍](https://www.jianshu.com/p/c5d333e6353c)

