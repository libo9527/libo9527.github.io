---
title: Study Notes of Git
description: Git 笔记
date: 2019-11-01 17:34:56
categories: Git
tags: Git
---

<img src="cover" width="100%"/>

<!-- more -->

## Checkout

- 拉取远程新分支到本地

  ```shell
  git checkout -b 本地分支名 origin/远程分支名
  ```
  
  另一种方式（这种方式不会切换到新分支，需要手动切换）：
  
  ```shell
  git fetch origin 远程分支名:本地分支名
  ```

## Stash

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

## Config

git 配置有 system 级别、global 和 local 三个，优先级依次递增，也就是配置冲突时优先使用 local 级别的。

- 查看当前仓库配置信息

  ```shell
  git config --local --list
  ```

## Remote

- 添加远程主机

  ```shell
  # git remote add <主机名> <网址>
  git remote add origin http://bitbucket.sstparts.com/scm/web/sst-cloud.git
  ```

  

## DIFFRENCE

1. pull VS fetch

   pull = fetch + merge

