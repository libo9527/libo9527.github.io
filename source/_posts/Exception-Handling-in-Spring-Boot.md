---
title: Exception Handling in Spring Boot
description: 统一异常处理
date: 2019-12-05 15:54:17
categories: Spring-Boot
tags: Exception
---

<img src="https://techblogstation.com/wp-content/uploads/2019/11/Spring-Boot-Exception-Handling.jpg" width="100%"/>

<!-- more -->

## 统一异常处理

> [Unified exception handling of Controller in Spring Boot ...](http://www.programmersought.com/article/4190609518/)
>
> [Spring Boot Exception Handling – Complete Tutorial](https://techblogstation.com/spring-boot/spring-boot-exception-handling/)

### 统一异常处理类

使用 Spring Boot 中的注解 @ControllerAdvice 和 @ExceptionHandler 即可

```java GlobalExceptionHandler.java
// RestControllerAdvice 用于全局返回 json，如需返回 ModelAndView 请使用 ControllerAdvice
@RestControllerAdvice
public class GlobalExceptionHandler {

  	// 处理自定义异常
    @ExceptionHandler(LoginException.class)
    public ReturnVo loginExceptionHandler(HttpServletRequest request, LoginException e) {
        e.printStackTrace();
        return ReturnVo.error(CodeEnum.LOGIN_ERROR);
    }

  	// 处理未知异常
    @ExceptionHandler(Exception.class)
    public ReturnVo unknownExceptionHandler(HttpServletRequest request, Exception e) {
        e.printStackTrace();
        return ReturnVo.error(CodeEnum.UNKNOWN_ERROR);
    }
}
```

### 自定义异常

> [自定义异常 | 廖雪峰](https://www.liaoxuefeng.com/wiki/1252599548343744/1264737765214592)

```java LoginException.java
public class LoginException extends BaseException {

}
```

