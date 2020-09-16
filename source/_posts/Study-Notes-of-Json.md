---
title: Study Notes of Json
description: Json 笔记
comments: false
hidden: false
top: false
date: 2020-09-16 14:01:39
categories:
tags:
---

<img src="https://i.ytimg.com/vi/0IoG-mSvWSo/maxresdefault.jpg" width="100%"/>

<!-- more -->

## Fastjson

### json string > List

```java
List<User> userList = JSON.parseArray(userStr, User.class);
```

### SimplePropertyPreFilter

#### 剔除某个属性

```java
// 去掉 product 中的 productText 字段
SimplePropertyPreFilter filter = new SimplePropertyPreFilter();
filter.getExcludes().add("productText");
JSON.toJSONString(product, filter);
```




