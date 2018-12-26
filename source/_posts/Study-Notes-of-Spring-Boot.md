---
title: Study Notes of Spring Boot
description: Makes it easy to create stand-alone, production-grade Spring-based Applications
date: 2018-05-31 10:17:55
categories:
  - Java
  - SpringBoot
tags:
  - SpringBoot
comments: false
---

<img src="https://www.zuojl.com/content/images/2018/11/spring-boot.png" width="100%"/>

<!-- more -->

# Spring Boot项目中设置Content-Type的方法

**一种比较简单优雅的方法是在注解@RequestMapping中添加produces参数即可**

```java
@RestController
@RequestMapping("/api")
public class ApiController {
    @RequestMapping(value = "/collection", produces="application/json;charset=UTF-8")
    public String home(String op) {
        
    }
```

