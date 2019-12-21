---
title: The Bast Practice of JWT
description: JWT 最佳实践
date: 2019-12-02 13:55:45
categories: JWT
tags: JWT
---

<img src="https://www.wangbase.com/blogimg/asset/201807/bg2018072301.jpg" width="100%"/>

<!-- more -->

> [官网](https://jwt.io/)
>
> [JSON Web Token 入门教程 | 阮一峰](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)
>
> [Supercharge Java Authentication with JSON Web Tokens (JWTs)](https://www.baeldung.com/java-json-web-tokens-jjwt)
>
> [Spring Boot Security 整合 JWT 实现 无状态的分布式API接口](https://juejin.im/post/5ca162b36fb9a05e181de126#heading-4)

## Java 中 JWT 的实现

- [java-jwt](https://github.com/auth0/java-jwt)
- [jjwt](https://github.com/jwtk/jjwt)

## java-jwt

> [java-jwt | GitHub](https://github.com/auth0/java-jwt)
>
> [Json Web Token的介绍和最佳实践](http://www.hyhblog.cn/2017/10/21/json-web-token-intro-and-best-practices/)
>
> [如何在SpringBoot中集成JWT(JSON Web Token)鉴权](https://juejin.im/post/5c8f50e35188252d9209588d#heading-10)

1. 导入依赖

   ```xml
   <dependency>
     <groupId>com.auth0</groupId>
     <artifactId>java-jwt</artifactId>
     <version>3.8.3</version>
   </dependency>
   ```

2. 生成 token

   ```java
   private static final String KEY = "secret";
   private static Algorithm ALGORITHM = Algorithm.HMAC256(KEY);
   String token = JWT.create()
     .withIssuer("gzhennaxia") // 签发人
     .withSubject(userId) // 主题，需要全局唯一
     .sign(ALGORITHM);
   ```

3. 验证 token

   ```java
   JWTVerifier verifier = JWT.require(ALGORITHM).build();
   DecodedJWT decodedJWT = verifier.verify(token);
   ```

   DecodedJWT：

   ```json
   {
       "algorithm":"HS256",
       "claims":{
           "iss":{
               "null":false
           },
           "sub":{
               "null":false
           }
       },
       "header":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9",
       "issuer":"gzhennaxia",
       "payload":"eyJzdWIiOiIxIiwiaXNzIjoiZ3poZW5uYXhpYSJ9",
       "signature":"ItL7KsvAp4Zezk5N5uu1ayAh2HlHU2EgIW3NiSZw1f8",
       "subject":"1",
       "token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwiaXNzIjoiZ3poZW5uYXhpYSJ9.ItL7KsvAp4Zezk5N5uu1ayAh2HlHU2EgIW3NiSZw1f8",
       "type":"JWT"
   }
   ```

## jjwt

> [jjwt | GitHub](https://github.com/jwtk/jjwt)

1. 导入依赖

   ```xml
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

2. 生成 token

   ```java
   private static Key KEY = Keys.secretKeyFor(SignatureAlgorithm.HS256);
   Jwts.builder()
     .setIssuer("gzhennaxia")
     .setSubject(userId.toString())
     .signWith(KEY)
     .compact();
   ```

   ```
   token: eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnemhlbm5heGlhIiwic3ViIjoiMSJ9._Q0XHRhoS0a-ZA4pMzEmY2hShds86IrE5i4-XSj6sGI
   ```

3. 验证 token

   ```java
   Jwt jwt = Jwts.parser().setSigningKey(KEY).parse(token);
   ```

   Jwt：

   ```json
   {
       "header":{
           "alg":"HS256"
       },
       "body":{
           "iss":"gzhennaxia",
           "sub":"1"
       },
       "signature":"_Q0XHRhoS0a-ZA4pMzEmY2hShds86IrE5i4-XSj6sGI"
   }
   ```