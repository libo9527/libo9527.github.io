---
title: What is CORS
description: CORS 简介
date: 2019-12-10 12:00:34
categories: 
tags:
---

<img src="https://geekflare.com/wp-content/uploads/2019/11/cors-1200x385.jpg" width="100%"/>

<!-- more -->

> **跨域资源共享**（Cross-origin resource sharing，简称CORS），用于让网页的受限资源能够被其他[域名](https://zh.wikipedia.org/wiki/域名)的页面访问的一种机制。通过该机制，页面能够自由地使用不同域名（cross-origin）的图片、样式、脚本、iframes以及视频。 ——[维基百科](https://zh.wikipedia.org/zh-hans/跨來源資源共享)
>
> [HTTP访问控制（CORS） - HTTP | MDN](https://developer.mozilla.org/zh/docs/Web/HTTP/Access_control_CORS)
>
> [跨域资源共享CORS 详解- 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2016/04/cors.html)
>
> [跨域资源共享（CORS）详解](http://blog.jiayuanzhang.com/post/cors-tutorial/)

## CORS

### 简单请求和非简单请求

只要同时满足以下两大条件，就属于简单请求：

（1）请求方法是以下三种方法之一：

- HEAD
- GET
- POST

（2）HTTP的头信息不超出以下几种字段：

- Accept
- Accept-Language
- Content-Language
- Last-Event-ID
- Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

## Spring 项目中配置 CORS

> [Enabling Cross Origin Requests for a RESTful Web Service | Spring](https://link.jianshu.com/?t=http://spring.io/guides/gs/rest-service-cors/)
>
> [CORS support in Spring Framework | Spring](https://spring.io/blog/2015/06/08/cors-support-in-spring-framework)

### 自定义过滤器

```java
//1. 使用过滤器注册器注册
@Configuration
public class FilterRegisterConfig {

    @Bean
    public FilterRegistrationBean filterRegisterBean() {
        FilterRegistrationBean<CorsFilter> registrationBean = new FilterRegistrationBean<>();
        registrationBean.setFilter(new CorsFilter());
        registrationBean.setUrlPatterns(new ArrayList<String>() {{ add("*"); }});
        return registrationBean;
    }
}
```

```java
//2. 使用 @WebFilter 注解
@Configuration
@WebFilter(urlPatterns = "*")
public class CorsFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse) servletResponse;
        response.setHeader("Access-Control-Allow-Origin", "http://localhost:9528");
        response.setHeader("Access-Control-Max-Age", "10");
        response.setHeader("Access-Control-Allow-Methods", "*");
        response.setHeader("Access-Control-Allow-Headers", "*");
        filterChain.doFilter(servletRequest, servletResponse);
    }
}
```

- **Access-Control-Allow-Methods**

  该字段必需，它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的请求类型。

- **Access-Control-Allow-Headers**

  如果浏览器请求包括`Access-Control-Request-Headers`字段，则`Access-Control-Allow-Headers`字段是必需的。它也是一个逗号分隔的字符串，表明服务器支持的所有头信息字段，不限于浏览器在"预检"中请求的字段。

- **Access-Control-Max-Age**

  该字段可选，用来指定本次预检请求的有效期，单位为秒。上面结果中，有效期是 10秒，即允许缓存该条回应 10 秒，在此期间，不用发出另一条预检请求。

  注意：如果设置缓存后，发现每次还是会发送 OPTIONS 请求，请检查是不是勾选了“禁止缓存”。

  ![](https://www.technipages.com/wp-content/uploads/2016/05/Chrome-Disable-Cache.png)

### 注解 `@CrossOrigin`

```javascript
// 当前 Controller 中所有的方法接受跨域访问
@CrossOrigin(origins = "http://localhost:9528", maxAge = 10)
@RestController
public class CorsController {

    @PostMapping("cors")
    public String cors() {
        return "success";
    }
}
```

```java
// 仅 /cors 可以接受跨域访问
@RestController
public class CorsController {
		@CrossOrigin(origins = "http://localhost:9528", maxAge = 10)
    @PostMapping("cors")
    public String cors() {
        return "cors";
    }

    @PostMapping("cors2")
    public String cors2(){
        return "cors2";
    }
}
```

### 实现 WebMvcConfigurer 接口

```java
@Configuration
public class WebMvcConfigurerImpl implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
      	// Error: registry.addMapping("*")
        registry.addMapping("/**")
                .allowedOrigins("http://localhost:9528")
                .allowedMethods("*")
                .allowedHeaders("*")
                .maxAge(10L);
    }
}
```

### Spring Actuator 项目可使用 application.properties 来配置

> [Spring Boot enabling CORS by application.properties](https://stackoverflow.com/questions/42874351/spring-boot-enabling-cors-by-application-properties)

```properties
endpoints.cors.allowed-origins=http://localhost:9528
endpoints.cors.allowed-methods=*
endpoints.cors.allowed-headers=*
endpoints.cors.max-age=10
```

## Spring Security 项目中配置 CORS

> [CORS | Spring Security](https://docs.spring.io/spring-security/site/docs/4.2.x/reference/html/cors.html)
>
> [Spring security CORS Filter | Stack Overflow](https://stackoverflow.com/questions/40418441/spring-security-cors-filter)
>
> [Spring Security 环境下 CORS 跨域失效问题](http://www.jetchen.cn/spring-security-cors/)
>
> [Spring Security配置cors跨域访问](http://www.saily.top/2016/12/29/spring-security-cors/)
>
> [CORS with Spring Boot and Spring Security](https://www.jianshu.com/p/87e1ef68794c)

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // by default uses a Bean by the name of corsConfigurationSource
        http.cors();
    }

    @Bean
    CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.setAllowedOrigins(Arrays.asList("http://localhost:9528"));
        configuration.setAllowedMethods(Arrays.asList("*"));
        configuration.setAllowedHeaders(Arrays.asList("*"));
        configuration.setMaxAge(10L);
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);
        return source;
    }
}
```



