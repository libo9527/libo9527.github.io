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

注意：如果配置了全局异常处理器，权限异常处理器就会失效，权限异常会先被全局异常处理器处理而到不了权限异常处理器。解决办法参考[这里](https://libo9527.github.io/2019/12/10/AccessDeniedHandler-of-Spring-Security-not-Working-While-GlobalExceptionHandler-existing/)

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

## 整合 JWT

> [SpringBootSecurity学习（13）前后端分离版之JWT](https://aijishu.com/a/1060000000015716)
>
> [jjwt | GitHub](https://github.com/jwtk/jjwt#table-of-contents)

使用 session 和 cookie 来维护登录状态的扩展性不好，使用 JWT 的 token 验证方式则可以很好的适应分布式场景的需求。

### 导入 jjwt 包

```xml pom.xml
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt-api</artifactId>
  <version>0.10.7</version>
</dependency>
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt-impl</artifactId>
  <version>0.10.7</version>
  <scope>runtime</scope>
</dependency>
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt-jackson</artifactId>
  <version>0.10.7</version>
  <scope>runtime</scope>
</dependency>
<!-- Uncomment this next dependency if you want to use RSASSA-PSS (PS256, PS384, PS512) algorithms:
<dependency>
    <groupId>org.bouncycastle</groupId>
    <artifactId>bcprov-jdk15on</artifactId>
    <version>1.60</version>
    <scope>runtime</scope>
</dependency>
-->
```

### 关闭 Session

```java SecurityConfigurerImpl.java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true, jsr250Enabled = true)
public class SecurityConfigurerImpl extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
    }
}
```

**源码截取**

```java SessionCreationPolicy.java
public enum SessionCreationPolicy {
	/** Always create an {@link HttpSession} */
	ALWAYS,
	/**
	 * Spring Security will never create an {@link HttpSession}, but will use the
	 * {@link HttpSession} if it already exists
	 */
	NEVER,
	/** Spring Security will only create an {@link HttpSession} if required */
	IF_REQUIRED,
	/**
	 * Spring Security will never create an {@link HttpSession} and it will never use it
	 * to obtain the {@link SecurityContext}
	 */
	STATELESS
}
```

### JWT 工具类

```java JWTUtil.java
public class JWTUtil {

    private static final Key KEY = Keys.secretKeyFor(SignatureAlgorithm.HS256);

    public final static String HEADER_TOKEN_NAME = "Authentication-Token";

    public static String genericToken(SysUser user) {
        user.setPassword(null);
        return Jwts.builder()
                .setIssuer("gzhennaxia")
                .setIssuedAt(new Date())
                .claim("user", JSON.toJSONString(user))
                .signWith(KEY)
                .compact();
    }

    public static SysUser parseToken2User(String token) {
        Jws<Claims> jws = Jwts.parser()
                .requireIssuer("gzhennaxia")
                .setSigningKey(KEY)
                .parseClaimsJws(token);
        return JSON.parseObject(jws.getBody().get("user").toString(), SysUser.class);
    }
}
```

### 修改登录成功处理器

```java AuthenticationSuccessHandlerImpl.java
@Component
public class AuthenticationSuccessHandlerImpl implements AuthenticationSuccessHandler {
    @Override
    public void onAuthenticationSuccess(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Authentication authentication) throws IOException, ServletException {
        User user = (User) authentication.getPrincipal();
        httpServletResponse.setHeader(JWTUtil.HEADER_TOKEN_NAME, JWTUtil.genericToken(SysUser.builder().username(user.getUsername()).build()));
        httpServletResponse.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
        httpServletResponse.getWriter().write(ReturnVo.errorInJson(CodeEnum.SUCCESS));
    }
}
```

### 配置 JWT 过滤器

```java JWTAuthenticationFilter.java
@Component
public class JWTAuthenticationFilter extends GenericFilterBean {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        String token = request.getHeader(JWTUtil.HEADER_TOKEN_NAME);
        if (!StringUtils.isEmpty(token)) {
            try {
                SysUser sysUser = JWTUtil.parseToken2User(token);
                SecurityContextHolder.getContext().setAuthentication(new UsernamePasswordAuthenticationToken(sysUser, null, sysUser.getAuthorities()));
            } catch (Exception e) {
                e.printStackTrace();
                servletResponse.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
                if (e instanceof MissingClaimException) {
                    servletResponse.getWriter().write(ReturnVo.errorInJson(CodeEnum.MISSING_CLAIM));
                } else if (e instanceof IncorrectClaimException) {
                    servletResponse.getWriter().write(ReturnVo.errorInJson(CodeEnum.INCORRECT_CLAIM));
                } else {
                    servletResponse.getWriter().write(ReturnVo.errorInJson(CodeEnum.UNKNOWN_PARSE_ERROR));
                }
                return;
            }
        }
        filterChain.doFilter(servletRequest, servletResponse);
    }
}
```

```java
@Configuration
public class SecurityConfigurerImpl extends WebSecurityConfigurerAdapter {

    @Autowired
    private JWTAuthenticationFilter jwtAuthenticationFilter;
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
    }
}
```

## 使用 JSON 格式数据登陆

> [Spring Security 登录使用 JSON 格式数据](http://www.javaboy.org/2019/0613/springsecurity-json.html)
>
> [Spring Security配置JSON登录](https://www.jianshu.com/p/693914564406)

Spring Security 默认支持的登陆方式为 formLogin，即 Content-Type 为`application/x-www-form-urlencoded`，如果想要使用 JSON 数据登陆，则需要自定义提取用户名密码的过滤器来替换默认的`UsernamePasswordAuthenticationFilter`。

```java CustomerAuthenticationFilter.java
public class CustomerAuthenticationFilter extends UsernamePasswordAuthenticationFilter {

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
        String contentType = request.getContentType();
        if (contentType.equals(MediaType.APPLICATION_JSON_VALUE) || contentType.equals(MediaType.APPLICATION_JSON_UTF8_VALUE)) {
            UsernamePasswordAuthenticationToken authRequest;
            try (InputStream in = request.getInputStream()) {
                SysUser user = JSON.parseObject(in, SysUser.class);
                authRequest = new UsernamePasswordAuthenticationToken(user.getUsername(), user.getPassword());
            } catch (IOException e) {
                e.printStackTrace();
                authRequest = new UsernamePasswordAuthenticationToken("", "");
            }
            return this.getAuthenticationManager().authenticate(authRequest);
        }
        return super.attemptAuthentication(request, response);
    }
}
```

```java SecurityConfig.java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true, jsr250Enabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private AuthenticationSuccessHandler authenticationSuccessHandler;

    @Autowired
    private AuthenticationFailureHandler authenticationFailureHandler;
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.addFilterAt(this.customerAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
    }

    @Bean
    CustomerAuthenticationFilter customerAuthenticationFilter() throws Exception {
        CustomerAuthenticationFilter filter = new CustomerAuthenticationFilter();
        filter.setRequiresAuthenticationRequestMatcher(new AntPathRequestMatcher("/user/login", "POST"));
        filter.setAuthenticationSuccessHandler(authenticationSuccessHandler);
        filter.setAuthenticationFailureHandler(authenticationFailureHandler);
        filter.setAuthenticationManager(this.authenticationManagerBean());
        return filter;
    }
}
```

注意：

1. 处理登陆请求的 URL 地址不能通过之前的方式（`.formLogin().loginProcessingUrl("/user/login")`）来配置，需要在自定义过滤器里配置，因为以前的方式配置的是默认的过滤器的，自定义过滤器会替换掉默认的，所以用以前的方式配置的 loginProcessingUrl 就失效了。
2. 自定义的过滤器之所以要在 security 配置类中初始化（使用`@Bean`的方式而不是直接在类上使用`@Component`），是因为需要使用`WebSecurityConfigurerAdapter`的`authenticationManagerBean()`方法来调用默认的认证管理器，否则需要自定义自己的认证管理器。

## 自定义认证管理器

为了把提取用户名密码的过滤器从配置类中抽离出来，就需要使用自定义的认证管理器来替换默认的认证管理器（`WebSecurityConfigurerAdapter.AuthenticationManagerDelegator`）。由于默认的认证管理器是不可继承的（由`final`修饰），所以通过实现`AuthenticationManager`接口来自定义，而认证管理器又是通过认证处理器`AuthenticationManager`提供的认证方法`authenticate()`最终对用户进行认证的，所以在自定义认证管理器的时候还需要提供认证处理器，可以在认证管理器的构造方法中直接初始化认证处理器，这里使用默认的持久层认证处理器为`DaoAuthenticationProvider`。

```java CustomerAuthenticationManagerImpl.java
@Component
public class CustomerAuthenticationManagerImpl implements AuthenticationManager {

    private final AuthenticationProvider provider;

    public CustomerAuthenticationManagerImpl(SecurityUserDetailService userDetailsService) {
        DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
        provider.setUserDetailsService(userDetailsService);
        provider.setPasswordEncoder(new BCryptPasswordEncoder());
        this.provider = provider;
    }

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        Authentication authenticate = this.provider.authenticate(authentication);
        if (Objects.nonNull(authenticate)) {
            return authenticate;
        }
        throw new ProviderNotFoundException("Authentication failed!");
    }
}
```

```java SecurityConfig.java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true, jsr250Enabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private CustomerAuthenticationFilter authenticationFilter;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.addFilterAt(authenticationFilter, UsernamePasswordAuthenticationFilter.class);
    }
}
```

注意：和自定义用于提取用户名密码的过滤器道理一样，因为是替换默认的认证处理器，所以 UserDetailsService、PasswordEncoder 等配置需要在初始化认证处理器的地方进行配置，而不能用以前配置默认认证处理器的方式。

- 配置默认处理器所需的 UserDetailsService：

  ```java SecurityConfig.java
  @Configuration
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
      @Autowired
      private SecurityUserDetailService userDetailService;

      @Override
      protected void configure(AuthenticationManagerBuilder auth) throws Exception {
          auth.userDetailsService(userDetailService);
      } 
  }
  ```

- 配置默认处理器所需的 PasswordEncoder：
  1. @Bean
  
    ```java SecurityConfig.java
      @Configuration
      public class SecurityConfig extends WebSecurityConfigurerAdapter {

        @Bean
          PasswordEncoder passwordEncoder() {
              return new BCryptPasswordEncoder();
          }
      }
    ```
  2. 在密码前加上加密算法前缀，如`{bcrypt}$2a$10$ffHsT7aCssLbKn7zACwOYupOQtxpzRVvIXD8Pveo9rHU4DrSUUyve`

## 自定义认证处理器

如果不想在自定义的认证管理器中使用默认的认证处理器，也可以自定义认证处理器。

```java
@Component
public class CustomerAuthenticationProviderImpl extends DaoAuthenticationProvider {

    @Autowired
    UserService userService;

    private static final PasswordEncoder PASSWORD_ENCODER = new BCryptPasswordEncoder();

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        String username = (String) authentication.getPrincipal();
        String password = (String) authentication.getCredentials();
        SysUser user = userService.selectUserByUsername(username);
        user.setRoleList(Collections.singletonList(new SysRole("user")));
        if (PASSWORD_ENCODER.matches(password, user.getPassword())) {
            return new UsernamePasswordAuthenticationToken(username, password, user.getRoleList());
        } else {
            throw new BadCredentialsException(this.messages.getMessage(
                    "AbstractUserDetailsAuthenticationProvider.badCredentials",
                    "Bad credentials"));
        }
    }

    @Override
    protected void doAfterPropertiesSet() {
    }
}
```

```java CustomerAuthenticationManagerImpl.java
@Component
public class CustomerAuthenticationManagerImpl implements AuthenticationManager {

    private final AuthenticationProvider provider;

    public CustomerAuthenticationManagerImpl(AuthenticationProvider provider) {
        this.provider = provider;
    }

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        Authentication authenticate = this.provider.authenticate(authentication);
        if (Objects.nonNull(authenticate)) {
            return authenticate;
        }
        throw new ProviderNotFoundException("Authentication failed!");
    }
}
```

