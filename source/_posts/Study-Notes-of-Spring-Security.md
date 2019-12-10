---
title: Study Notes of Spring Security
description: Spring Security 笔记
date: 2019-12-05 18:31:03
categories: Spring-Security
tags: Spring-Security
---

<img src="https://miro.medium.com/max/2560/1*bQV578ujCEPGvy9ZVemdUg.jpeg" width="100%"/>

<!-- more -->

> 版本说明：
>
> Spring Boot：2.2.1.RELEASE
>
> Spring Security：

## Spring Security 简介

> [Spring Security参考 | Spring Cloud 中文网](https://www.springcloud.cc/spring-security.html)

## 初级使用：默认用户认证

1. 添加依赖

   ```xml
   <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
   ```

   只要加入依赖，项目中所有接口都会被自动保护起来。默认用户名为：user，密码在项目启动后自动生成。

   ```
   Using generated security password: 6c920ced-f1c1-4604-96f7-f0ce4e46f5d4
   ```

   也可以通过配置文件`application.properties`指定用户名密码

   ```properties
   spring.security.user.username=admin
   spring.security.user.username=123456
   ```

2. 测试接口

   ```java
   @RestController
   public class HelloController {
       @GetMapping("/hello")
       public String hello() {
           return "hello";
       }
   }
   ```

3. 访问接口，将重定向到默认登陆页面，登陆成功之后，就会自动跳转到原来都接口。

   ![](http://www.javaboy.org/images/boot/25-2.png)

   另外：也可以使用 postman 来发送登陆请求（需要将登陆信息放在请求头中，避免重定向到登陆页面）

   ![](http://www.javaboy.org/images/boot/25-3.png)

   通过以上两种不同的登录方式，可以看出，Spring Security 支持 form 表单和 HttpBasic 两种不同的认证方式，对应到源码是`.formLogin()`和`.httpBasic()`：

   ```java
   public abstract class WebSecurityConfigurerAdapter implements WebSecurityConfigurer<WebSecurity> {
       protected void configure(HttpSecurity http) throws Exception {
           this.logger.debug("Using default configure(HttpSecurity). If subclassed this will potentially override subclass configure(HttpSecurity).");
           ((HttpSecurity) ((HttpSecurity) ((AuthorizedUrl) http.authorizeRequests()
                   .anyRequest())
                   .authenticated()
                   .and())
                   .formLogin()
                   .and())
                   .httpBasic();
       }
   }
   ```


## 中级使用：内存用户认证

创建 Security 配置类

```java SecurityConfig
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        PasswordEncoder encoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
        // 在内存中认证
        auth.inMemoryAuthentication()
                .withUser("admin").password(encoder.encode("admin")).roles("admin")
                .and()
                .withUser("user").password(encoder.encode("user")).roles("user");
    }
}
```

## 高级使用：数据库用户认证

实现 UserDetailsService 接口

```java 
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private UserService userService;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userService.selectUserByUsername(username);
        if (user == null){
            throw new UsernameNotFoundException("Username not exist!");
        }
        List<GrantedAuthority> authorities = new ArrayList<>();
        authorities.add(new SimpleGrantedAuthority("user"));
        return new org.springframework.security.core.userdetails.User(username, user.getPassword(), authorities);
    }
}
```

```java SecurityConfig.java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private UserDetailService userDetailService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailService);
    }
}
```

## 前后端分离后的认证

> [SpringBootSecurity学习（12）前后端分离版之简单登录](https://aijishu.com/a/1060000000015544)

前后端分离后，后端不再需要提供登陆页面，只需要配置登陆地址即可。

### 开启授权认证

```java SecurityConfigurerImpl.java
@Configuration
public class SecurityConfigurerImpl extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        /*
         * 开启授权认证
         */
        http.authorizeRequests().anyRequest().authenticated();
    }
}
```

### 配置处理登陆请求的 API 地址

```java SecurityConfigurerImpl.java
@Configuration
public class SecurityConfigurerImpl extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        /*
         * 处理登陆请求的接口：/user/login
         * 登陆接口用户名参数名：username
         * 登陆接口密码参数名：password
         */
        http.formLogin().loginProcessingUrl("/user/login").usernameParameter("username").passwordParameter("password");
    }
}
```

### 配置登陆成功处理器

```java SecurityConfigurerImpl.java
@Configuration
public class SecurityConfigurerImpl extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        /*
         * 登陆成功处理器：登陆成功后直接返回 JSON 数据
         * 默认的成功处理器（SimpleUrlAuthenticationSuccessHandler）是进行页面跳转。
         */
        http.formLogin().successHandler(new AuthenticationSuccessHandler() {
            @Override
            public void onAuthenticationSuccess(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Authentication authentication) throws IOException, ServletException {
                httpServletResponse.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
                httpServletResponse.getWriter().write("{\"code\": \"200\", \"msg\": \"登录成功\"}");
            }
        });
    }
}
```

**默认登陆成功处理器源码截取**

```java SimpleUrlAuthenticationSuccessHandler.java
public class SimpleUrlAuthenticationSuccessHandler extends AbstractAuthenticationTargetUrlRequestHandler implements AuthenticationSuccessHandler {
    public SimpleUrlAuthenticationSuccessHandler() {
    }

    public SimpleUrlAuthenticationSuccessHandler(String defaultTargetUrl) {
        this.setDefaultTargetUrl(defaultTargetUrl);
    }

    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
        this.handle(request, response, authentication);
        this.clearAuthenticationAttributes(request);
    }

    protected final void clearAuthenticationAttributes(HttpServletRequest request) {
        HttpSession session = request.getSession(false);
        if (session != null) {
            session.removeAttribute("SPRING_SECURITY_LAST_EXCEPTION");
        }
    }
}
```

```java AbstractAuthenticationTargetUrlRequestHandler.java
public abstract class AbstractAuthenticationTargetUrlRequestHandler {

    protected void handle(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
        String targetUrl = this.determineTargetUrl(request, response, authentication);
        if (response.isCommitted()) {
            this.logger.debug("Response has already been committed. Unable to redirect to " + targetUrl);
        } else {
            this.redirectStrategy.sendRedirect(request, response, targetUrl);
        }
    }
}
```

### 配置登陆失败处理器

```java SecurityConfigurerImpl.java
@Configuration
public class SecurityConfigurerImpl extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        /*
         * 登陆失败处理器
         * 默认处理器（SimpleUrlAuthenticationFailureHandler）将重定向到登陆页面
         */
        http.formLogin().failureHandler(new AuthenticationFailureHandler() {
            @Override
            public void onAuthenticationFailure(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AuthenticationException e) throws IOException, ServletException {
                httpServletResponse.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
                httpServletResponse.getWriter().write("{\"code\": \"500\", \"msg\": \"登录失败\"}");
            }
        });
    }
}
```

**默认登陆失败处理器源码截取**

```java SimpleUrlAuthenticationFailureHandler.java
public class SimpleUrlAuthenticationFailureHandler implements AuthenticationFailureHandler {
  
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {
        if (this.defaultFailureUrl == null) {
            this.logger.debug("No failure URL set, sending 401 Unauthorized error");
            response.sendError(HttpStatus.UNAUTHORIZED.value(), HttpStatus.UNAUTHORIZED.getReasonPhrase());
        } else {
            this.saveException(request, exception);
            if (this.forwardToDestination) {
                this.logger.debug("Forwarding to " + this.defaultFailureUrl);
                request.getRequestDispatcher(this.defaultFailureUrl).forward(request, response);
            } else {
                this.logger.debug("Redirecting to " + this.defaultFailureUrl);
                this.redirectStrategy.sendRedirect(request, response, this.defaultFailureUrl);
            }
        }

    }

}
```

![](https://i.loli.net/2019/12/10/mDavjnKlXk5CQO8.png)

### 配置认证异常处理器

```java SecurityConfigurerImpl.java
@Configuration
public class SecurityConfigurerImpl extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        /*
         * 认证异常（未登陆/登陆过期等）处理器
         * 默认处理器（LoginUrlAuthenticationEntryPoint）将重定向到登陆页面
         */
        http.exceptionHandling().authenticationEntryPoint(new AuthenticationEntryPoint() {
            @Override
            public void commence(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AuthenticationException e) throws IOException, ServletException {
                httpServletResponse.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
                httpServletResponse.getWriter().write("{\"code\": \"401\", \"msg\": \"登陆已过期或未登陆\"}");
            }
        });
    }
}
```

**默认认证异常处理器源码截取**

```java LoginUrlAuthenticationEntryPoint.java
public class LoginUrlAuthenticationEntryPoint implements AuthenticationEntryPoint, InitializingBean {
	public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        String redirectUrl = null;
        if (this.useForward) {
            if (this.forceHttps && "http".equals(request.getScheme())) {
                redirectUrl = this.buildHttpsRedirectUrlForRequest(request);
            }

            if (redirectUrl == null) {
                String loginForm = this.determineUrlToUseForThisRequest(request, response, authException);
                if (logger.isDebugEnabled()) {
                    logger.debug("Server side forward to: " + loginForm);
                }

                RequestDispatcher dispatcher = request.getRequestDispatcher(loginForm);
                dispatcher.forward(request, response);
                return;
            }
        } else {
            redirectUrl = this.buildRedirectUrlToLoginPage(request, response, authException);
        }

        this.redirectStrategy.sendRedirect(request, response, redirectUrl);
    }
}
```

![](https://i.loli.net/2019/12/10/unCSeMidlUhOj8s.png)

### 配置权限异常处理器

```java SecurityConfigurerImpl.java
@Configuration
// 开启方法级别的权限注解
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true, jsr250Enabled = true)
public class SecurityConfigurerImpl extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        /*
         * 权限异常处理器
         * 默认处理器（AccessDeniedHandlerImpl）将返回状态码为 403 的响应
         */
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

接口方法上使用注解`@PreAuthorize`配置权限

```java
@RestController
@RequestMapping("hello")
public class HelloController {

    @Value("${spring.profiles.active}")
    private String environment;

    @GetMapping("env")
    @PreAuthorize("hasRole('user')")
    public String getEnvironment() {
        return "The Environment is " + environment;
    }
}
```

**默认权限异常处理器源码截取**

```java AccessDeniedHandlerImpl.java
public class AccessDeniedHandlerImpl implements AccessDeniedHandler {

    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        if (!response.isCommitted()) {
            if (this.errorPage != null) {
                request.setAttribute("SPRING_SECURITY_403_EXCEPTION", accessDeniedException);
                response.setStatus(HttpStatus.FORBIDDEN.value());
                RequestDispatcher dispatcher = request.getRequestDispatcher(this.errorPage);
                dispatcher.forward(request, response);
            } else {
                response.sendError(HttpStatus.FORBIDDEN.value(), HttpStatus.FORBIDDEN.getReasonPhrase());
            }
        }

    }
}
```

![](https://i.loli.net/2019/12/10/4eroj3GJS51gMP8.png)