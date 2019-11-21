---
title: Permission Control in Git Repository
description: Git 仓库中的权限控制
date: 2019-11-15 10:44:51
categories: Git
tags: Git
---

<img src="https://backlog.com/app/themes/backlog-child/assets/img/guides/git/basics/rewriting_history_005.png" width="100%"/>

<!-- more -->

> 原有的 Git 并不支持权限控制，权限控制都是各大远程仓库托管平台所提供的功能。

## 合并流程

> 我们公司 API 项目组的 master 分支的合并权限是测试人员才有的，也就是说只有测试人员才能把其他开发分支合并到 master 分支。

因为合并的时候如果有冲突，就需要到测试人员那里去解决冲突，但是测试那里并没有开发环境，解决冲突后无法确定项目是否能正常运行（如果合并时出现合并后存在问题的代码），而部署的权限也在测试手里，合完后他们就直接部署了。

这就存在合并后有冲突，冲突解决后如果代码存在运行时异常，直接部署后导致部署失败，又需要回退再重新合并。

为了解决这种麻烦且易错的问题，我的方案如下。

### 主要角色

1. QA：测试人员
2. DP：开发人员

### 涉及分支

1. master
2. API-1921（根据 jira 分配的任务编号建对应分支）

### 流程图

![](https://i.loli.net/2019/11/15/xvD9GbWqJ6rBAMk.png)

