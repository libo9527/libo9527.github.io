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

#### [Calendar API | Google Developers](https://developers.google.com/calendar)

##### ~~首页~~

##### 指南

###### ~~Get Started~~

###### [Authorizing Requests](https://developers.google.com/calendar/auth)

##### 参考

###### [CalendarList](https://developers.google.com/calendar/v3/reference/#CalendarList)

##### 示例

###### Java

##### 支持

#### [Google API Client Libraries > Java](https://developers.google.com/api-client-library/java)

##### APIs

###### [Calendar API Client Library for Java](https://github.com/googleapis/google-api-java-client-services/tree/master/clients/google-api-services-calendar/v3)

> [APIs Explorer for the Calendar API](https://developers.google.com/apis-explorer/#p/calendar/v1/)



## 身份验证

> [Using OAuth 2.0 to Access Google APIs](https://developers.google.com/identity/protocols/OAuth2)
>
> [Scenarios: Web server applications](https://developers.google.com/identity/protocols/OAuth2#webserver)
>
> [Authorization code flow | Google-API-Client-Library > java](https://developers.google.com/api-client-library/java/google-api-java-client/oauth2#authorization_code_flow)
>
> [OAuth 2.0 Playground | Google Developers](https://developers.google.com/oauthplayground)

认证流程图

![](https://developers.google.com/accounts/images/webflow.png)

{% tabs authorize %}

<!-- tab 1.获取凭证 → -->

在  [Google API Console](https://console.developers.google.com/) 中获取 OAuth 2.0 认证凭证(例如  client ID、client secret)。

<!-- endtab -->

<!-- tab 2.获取 code → -->

...

<!-- endtab -->



<!-- tab 3.获取 token → -->

> [ERROR: java.net.SocketTimeoutException: Connect timed out](https://stackoverflow.com/questions/37314744/google-drive-api-java-client-behind-proxy/39347143#39347143)



<!-- endtab -->



<!-- tab 4.调用 API → -->

> [Events: list ｜ Calendar API](https://developers.google.com/calendar/v3/reference/events/list)



<!-- endtab -->

{% endtabs %}



[Springboot 拦截器 + 自定义注解，实现权限控制](https://blog.csdn.net/sunnyzyq/article/details/95348788)

