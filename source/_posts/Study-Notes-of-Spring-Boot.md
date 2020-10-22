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

<img src="https://pic3.zhimg.com/v2-8315cb308b890c7087edfc088043f572_720w.jpg?source=172ae18b" width="100%"/>

<!-- more -->

## 配置

### 规范

> [史上最全面的Spring Boot配置文件详解](https://blog.csdn.net/qq_42564846/article/details/84566822)

配置文件中的字符串尽量不要有下划线。配置中 key 不能带下划线，value 可以。

### 多环境配置

> [Spring-Boot application.yml 文件拆分，实现 maven 多环境动态启用 Profiles](https://yulaiz.com/spring-boot-maven-profiles/)
>
> [多配置文件](https://blog.csdn.net/qq_42564846/article/details/84566822#t12)

## 注解

### @CrossOrigin

> [SpringBoot 实现前后端分离的跨域访问（CORS）](http://www.spring4all.com/article/177)

**什么是跨域HTTP请求**

> [跨域的那些事儿](https://zhuanlan.zhihu.com/p/28562290)

浏览器的同源策略会导致跨域，这里同源策略又分为以下两种

1. DOM 同源策略：禁止对不同源页面 DOM 进行操作。这里主要场景是 iframe 跨域的情况，不同域名的 iframe 是限制互相访问的。
2. XmlHttpRequest 同源策略：禁止使用 XHR 对象向不同源的服务器地址发起 HTTP 请求。

只要协议、域名、端口有任何一个不同，都被当作是不同的域，之间的请求就是跨域操作。

## 数据源

### [SpringBoot 中 JPA 的使用]()

## Spring Cache

1. @EnableCaching

   启动类上加该注解

2. 查询方法上添加注解`@Cacheable(value = "cacheName", key="#id")`，缓存本质是一个 map。

   ```java
   @Cacheable(value = "cacheName", key="#id")
   public String getXXX(int id) {
       ...
   }
   ```

3. 更新和删除方法上添加注解`@CacheEvict(value = “cacheName”, key = “#id”)`

   ```java
   @CacheEvict(value = "cacheName", key="#id")
   public void deleteXXX(int id) {
       ...
   }
   ```

**使用原则**：需要过期时间使用 redis，不需要过期时间使用 spring cache

## Spring Boot项目中设置Content-Type的方法

**一种比较简单优雅的方法是在注解@RequestMapping中添加produces参数即可**

```java
@RestController
@RequestMapping("/api")
public class ApiController {
    @RequestMapping(value = "/collection", produces="application/json;charset=UTF-8")
    public String home(String op) {
        
    }
```

## Spring Boot 的启动流程

1. 创建 `SpringApplication` 实例

   - 推断应用类型（SERVLET/REACTIVE/NONE）

     classpath 路径下如果存在 `javax.servlet.Servlet` 和 `org.springframework.web.context.ConfigurableWebApplicationContext` 这两个类，那么就认为是 SERVLET 类型。

   - 加载**应用上下文初始化器**(ApplicationContextInitializer)

     使用**Spring工厂加载器**(SpringFactoriesLoader) 加载 classpath 下所有的 ApplicationContextInitializer。

   - 加载**应用监听器**(ApplicationListener)

     使用**Spring工厂加载器**(SpringFactoriesLoader) 加载 classpath 下所有的 ApplicationListener。

   - 推断 main 方法所在类

     从方法调用栈中找出 main 方法所在类。

2. 调用 `SpringApplication` 实例的 `run()` 方法

   - 通过**应用事件广播器**(ApplicationEventMulticaster)广播 starting 事件

   - 创建并配置 Environment。

     比如 PropertySource（属性文件源）、profile

