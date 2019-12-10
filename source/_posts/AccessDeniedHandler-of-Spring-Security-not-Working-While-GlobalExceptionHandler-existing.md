---
title: >-
  AccessDeniedHandler of Spring Security not Working While
  GlobalExceptionHandler existing
description: 存在全局异常处理器的情况下，Sprint Security 的 AccessDeniedHandler 不起作用。
date: 2019-12-10 15:06:57
categories: Sprint-Security
tags: Sprint-Security
---

<img src="https://previews.123rf.com/images/asiln/asiln1408/asiln140800037/30823194-access-denied-stamp.jpg" width="100%"/>

<!-- more -->

> [AccessDeniedHandler and AuthenticationEntryPoint does not work Because of the global exception handler](https://github.com/spring-projects/spring-security/issues/6908)

## 问题描述

在学习 Spring Security 的过程中，配置了权限异常处理器，但没有起作用，查找后发现是因为全局异常处理器已经处理了该异常，导致异常并没有传递到该处理器。

### 全局异常处理器

```java GlobalExceptionHandler.java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ReturnVo unknownExceptionHandler(HttpServletRequest request, Exception e) {
        e.printStackTrace();
        return ReturnVo.error(CodeEnum.UNKNOWN_ERROR);
    }
}
```

### 权限异常处理器

```java SecurityConfig.java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true, jsr250Enabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        ......
          
        http.exceptionHandling().accessDeniedHandler(new AccessDeniedHandler() {
            @Override
            public void handle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AccessDeniedException e) throws IOException, ServletException {
                httpServletResponse.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
                httpServletResponse.getWriter().write("{\"code\": \"402\", \"msg\": \"权限不足\"}");
            }
        });
    }
}
```

## 解决方法

### 方法一：全局异常处理器中将该异常继续抛出

```java GlobalExceptionHandler.java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ReturnVo unknownExceptionHandler(HttpServletRequest request, Exception e) throws Exception {
        if (e instanceof AccessDeniedException){
            throw e;
        }
        e.printStackTrace();
        return ReturnVo.error(CodeEnum.UNKNOWN_ERROR);
    }
}
```



### 方法二：将该异常处理器定义到全局异常处理器对应的类中

```java GlobalExceptionHandler.java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(AccessDeniedException.class)
    public void loginExceptionHandler(HttpServletRequest request, HttpServletResponse response, AccessDeniedException e) throws IOException, ServletException {
        response.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
        response.getWriter().write("{\"code\": \"402\", \"msg\": \"权限不足\"}");
    }

    @ExceptionHandler(Exception.class)
    public ReturnVo unknownExceptionHandler(HttpServletRequest request, Exception e) {
        e.printStackTrace();
        return ReturnVo.error(CodeEnum.UNKNOWN_ERROR);
    }
}
```

