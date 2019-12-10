---
title: Spring Boot Hot Swap with Intellij IDEA
description: Spring Boot 热部署/热重启/热加载
date: 2019-12-04 13:39:41
categories: Spring-Boot
tags: Hot-Swap
---

<img src="https://i.ytimg.com/vi/VWF7vCJSqrA/maxresdefault.jpg" width="100%"/>

<!-- more -->

## Spring Boot 热部署

> [Spring Boot Application Live Reload (Hot Swap) With Intellij IDEA](https://dzone.com/articles/spring-boot-application-live-reload-hot-swap-with)
>
> [Spring Boot 101 (Part 8) - Live Reload / Hot Swap | YouTube](https://www.youtube.com/watch?v=VWF7vCJSqrA)
>
> [springboot idea 热部署配置](https://blog.csdn.net/sdaujsj1/article/details/101992877)

1. 添加 devtool 依赖

   ```xml
   <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-devtools</artifactId>
     <optional>true</optional>
   </dependency>
   ```

   注意：还需要添加 maven 插件，否则无效

   ```xml
   <plugin>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-maven-plugin</artifactId>
     <configuration>
       <fork>true</fork>
     </configuration>
   </plugin>
   ```

2. IDEA 开启自动编译

   Settings --> Build, Execution, Deployment --> Compiler --> Build project automatically

3. IDEA 开启 automake 功能

   `CTRL + SHIFT + ALT + / ` --> Registry --> compiler.automake.allow.when.app.running

   注意：调整触发热部署的延迟时间

   compiler.automake.trigger.delay

   compiler.document.save.trigger.delay

4. IDEA 配置更新时触发事件

   Edit Configuration --> On 'Update' action --> Update classes and resources

   注意：官方不推荐设置在失焦(On frame deactivation)的时候更新