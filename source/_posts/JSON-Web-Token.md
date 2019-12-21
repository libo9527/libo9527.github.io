---
title: JSON Web Token
description: JWT
date: 2019-12-17 11:25:16
categories: JWT
tags: JWT
---

<img src="https://images-1254006831.cos.ap-chengdu.myqcloud.com/jwt.jpg" width="100%"/>

<!-- more -->

## WHAT IS JWT

## JWT 缺点

> [Stop using JWT for sessions](http://cryto.net/~joepie91/blog/2016/06/13/stop-using-jwt-for-sessions/)
>
> [（译）别再使用 JWT 作为 Session 系统！问题重重且很危险。](https://learnku.com/articles/22616#6536ec)
>
> [How to destroy JWT Tokens on logout?](https://stackoverflow.com/questions/37959945/how-to-destroy-jwt-tokens-on-logout)

由于是无状态的，所以服务器无法主动销毁未过期的 JWT-Token。补救措施：将无效但未过期的 token 存在黑名单中，每次验证 token 前先检查其是否在黑名单中，在就直接抛相应异常即可。

