---
title: SpringBoot学习笔记一
categories:
  - Java
  - SpringBoot
tags:
  - SpringBoot
comments: false
date: 2018-05-31 10:17:55
---


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

