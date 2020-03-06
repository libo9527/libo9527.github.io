---
title: Study Notes of Google Calendar API
description: 谷歌 API 学习笔记
comments: false
hidden: true
top: false
date: 2020-03-02 15:54:53
categories: 
- Study Notes
- Google Calendar API
tags:
---

<img src="https://www.techapple.com/wp-content/uploads/2017/10/GOOGLE-CALENDER.jpg" width="100%"/>

<!-- more -->

## 任务安排

### 阅读官方文档

> [Calendar API | Google Developers](https://developers.google.com/calendar)

1. ~~首页~~
2. 指南
   1. ~~Get Started~~
   2. [Authorizing Requests](https://developers.google.com/calendar/auth)
3. 参考
4. 示例
   1. Java
5. 支持

[Google API Client Libraries > Java](https://developers.google.com/api-client-library/java)

## 身份验证

> [Using OAuth 2.0 to Access Google APIs](https://developers.google.com/identity/protocols/OAuth2)
>
> [Scenarios: Web server applications](https://developers.google.com/identity/protocols/OAuth2#webserver)

认证流程图

![](https://developers.google.com/accounts/images/webflow.png)


```
{% tabs authorize %}

<!-- tab 1.获取凭证 → -->

在  [Google API Console](https://console.developers.google.com/) 中获取 OAuth 2.0 认证凭证(例如  client ID、client secret)。

<!-- endtab -->

<!-- tab 2.获取code → -->

ad

<!-- endtab -->

{% endtab %}
```
