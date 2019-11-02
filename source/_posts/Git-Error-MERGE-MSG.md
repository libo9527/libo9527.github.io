---
title: Git Error MERGE_MSG
description: Git Error
date: 2019-11-01 19:19:26
categories: Git
tags: Git
---

<img src="https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/95e1e0d779e7824420350bfbc96019a1.png" width="100%"/>

<!-- more -->

> [Git-解释“Swap file .MERGE_MSG.swp already exists”的问题- 段浅浅的 ...](https://blog.csdn.net/qq_32452623/article/details/78395832)

合并后，执行 git pull 或者 push 或者 merge 命令时，报以上错误

## 原因

merge 后未正确关闭 MERGE_MSG 编辑文件。合并后会进入 commit msg 编辑页面（MERGE_MSG文件），若未正确退出，下次再合并时就会报这个错误。

![](https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/9a65e381933b29e2d6dd12bcaa50dccf.png)

这是因为 linux 中使用 vim 编辑某文件时，会生成一个 .sw* 的隐藏文件，当正确退出后会自动删除该文件。

## 解决

1. 回到合并前状态

   ```shell
   git merge -abort
   ```

2. 删除 vim 非正常关闭产生的文件

   ```shell
   rm .git/.MERGE_MSG.sw*
   ```

3. 重新合并