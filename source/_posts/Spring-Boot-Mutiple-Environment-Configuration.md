---
title: Spring-Boot Mutiple Environment Configuration
description: Spring Boot 多环境配置
date: 2019-11-28 14:06:13
categories: Spring-Boot
tags: Spring-Boot
---

<img src="https://miamioh.edu/_files/images/it-services/news-articles/2017/08/dev-graphic.png" width="100%"/>

<!-- more -->

> [Spring Boot 多环境配置](http://www.jerome.xin/articles/spring-boot-multi-environment-configuration)

## application.yml

```yaml
# common properties
spring:
  profiles:
    active: @profiles.active@
```



## application-dev.yml

```yaml
# dev properties
spring:
  profiles: dev
```



## application-test.yml

```yaml
# test properties
spring:
  profiles: test
```



## application-prod.yml

```yaml
# prod properties
spring:
  profiles: prod
```



## pom.xml

```xml
<dependencies></dependencies>

    <profiles>
        <!-- Development environment -->
        <profile>
            <id>dev</id>
            <properties>
                <profiles.active>dev</profiles.active>
            </properties>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
        </profile>
        <!-- Test environment -->
        <profile>
            <id>test</id>
            <properties>
                <profiles.active>test</profiles.active>
            </properties>
        </profile>
        <!-- Production environment -->
        <profile>
            <id>prod</id>
            <properties>
                <profiles.active>prod</profiles.active>
            </properties>
        </profile>
    </profiles>

<build></build>
```

## HelloController.java

```java
package com.gzhennaxia.admin.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author bo li
 * @date 2019-11-28 14:28
 */
@RestController
@RequestMapping("hello")
public class HelloController {

    @Value("${spring.profiles.active}")
    private String environment;

    @GetMapping("env")
    public String getEnvironment() {
        return "The Environment is " + environment;
    }
}
```

![](https://i.loli.net/2019/11/28/zqRYIDZbMgh7JLs.png)

## 环境切换

### 默认环境

在项目的`pom.xml`中，使用`<activeByDefault>true</activeByDefault>`这段配置，定义了`dev`为项目的默认环境。所以当开发中启动项目会默认使用`application-dev.yml`的配置。在控制台的日志的第二行会有这么一段日志`The following profiles are active: dev`，表示已经加载了开发环境相关的配置。

### 通过`Maven`打包时指定环境

使用`Maven`命令`mvn clean package -Pprod`对项目的进行打包，然后直接使用`java -jar xxx.jar`命令启动项目。会发现日志输出了`The following profiles are active: prod`，说明使用的是生产环境。

### 使用命令启动时指定环境

首先使用`Maven`命令`mvn clean package`对项目进行打包，然后先使用`java -jar xxx.jar`命令启动项目。会发现日志输出了`The following profiles are active: dev`，说明启动的是开发环境。接下来使用`java -jar xxx.jar --spring.profiles.active=test`命令启动项目，会发现日志输出了`The following profiles are active: test`。