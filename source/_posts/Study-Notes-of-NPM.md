---
title: Study Notes of NPM
description: npm 笔记
date: 2019-08-30 10:38:33
categories: npm
tags: npm
---

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/db/Npm-logo.svg/1200px-Npm-logo.svg.png" width="100%"/>

<!-- more -->

## 命令

### init

快速生成`package.json`文件

```shell
# --yes 参数能够帮助我们快速生成默认的package.json
npm init --yes
```

## npm 运行模块命令

> [关于局部安装npm模块找不到命令的问题分析及解决方案](https://blog.csdn.net/qq_21998595/article/details/80460673)

命令行运行 npm 局部安装的包：

1. 在相应命令前加上路径 `node_modules/.bin/`

   例如：`node_modules/.bin/lessc 1-nest.less test.css`

2. package.json 中配置 scripts

   ```json
   "scripts": {
     "lessc": "less index.js"
   }
   ```

   