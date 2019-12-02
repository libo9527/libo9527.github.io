---
title: Study Notes of Git
description: Git 笔记
date: 2019-11-01 17:34:56
categories: Git
tags: Git
---

<img src="https://www.linkedin.com/media-proxy/ext?w=1200&h=675&hash=EnUu%2BT9OJGbHiVWYjgwyvE0dids%3D&ora=1%2CaFBCTXdkRmpGL2lvQUFBPQ%2CxAVta5g-0R6plxVUzgUv5K_PrkC9q0RIUJDPBy-kWyaq89KIf3LpesfWfrq8pVgTcCkFkgw1fOuuSC3gFNu1NdCvKg" width="100%"/>

<!-- more -->

## Command


### remote

- 添加远程主机

  ```shell
  # git remote add <主机名> <网址>
  git remote add origin http://bitbucket.sstparts.com/scm/web/sst-cloud.git
  ```

### branch

- 查看本地分支与远程分支的追踪关系

  > -v, -vv, --verbose
  > When in list mode, show sha1 and commit subject line for each head, along with relationship to upstream branch (if any). If given twice, print the name of the upstream branch, as well (see also git remote show `<remote>`).
  
  ```shell
  git branch -vv
  ```

- 关联远程分支

  >  -u `<upstream>`, --set-upstream-to=`<upstream>`
  > Set up `<branchname>`'s tracking information so `<upstream>` is considered `<branchname>`'s upstream branch. If no `<branchname>` is specified, then it defaults to the current branch.
  
  ```shell
  git branch --set-upstream-to=master
  # or
  git bracch -u master
  ```
  
  

### checkout

- 拉取远程新分支到本地

  ```shell
  git checkout -b 本地分支名 origin/远程分支名
  ```
  另一种方式（这种方式不会切换到新分支，需要手动切换）：
  
  ```shell
  git fetch origin 远程分支名:本地分支名
  ```

### Stash

- 查看 stash 列表

  ```shell
  git stash list
  ```

- 清空 stash 列表

  ```shell
  git stash clear
  ```

- 删除队列中的第一个

  ```shell
  git stash drop stash@{0}
  ```

### Config

git 配置有 system 级别、global 和 local 三个，优先级依次递增，也就是配置冲突时优先使用 local 级别的。

- 查看当前仓库配置信息

  ```shell
  git config --local --list
  ```

## DIFFRENCE

1. pull VS fetch

   pull = fetch + merge

