---
title: Study Notes of Sqlite
description: 一句话描述这篇文章
comments: false
hidden: false
top: false
date: 2020-03-21 15:53:14
categories:
tags:
---

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/38/SQLite370.svg/1200px-SQLite370.svg.png" width="100%"/>

<!-- more -->

> [SELECT from nothing?](https://stackoverflow.com/questions/3732422/select-from-nothing)

It's not consistent across vendors - Oracle, MySQL, and DB2 support dual:

```sql
SELECT 'Hello world'
  FROM DUAL
```

...while SQL Server, PostgreSQL, and SQLite don't require the `FROM DUAL`:

```sql
SELECT 'Hello world'
```

MySQL does support both ways.

> [mybatis batch insert oracle table ID use sequence return ID NULL](https://github.com/mybatis/mybatis-3/issues/864)
>
> [MyBatis 3.3.1 批量插入多行回写自增id](https://blog.csdn.net/top_code/article/details/52404345)

```xml
<insert id="batchInsert" useGeneratedKeys="true" keyProperty="id">
  INSERT INTO tb_user
  (name, password,age, email,gender,register_time)
  VALUES
  <foreach collection="list" item="user" index="index" separator="," >
    (#{user.name},#{user.password},#{user.age},#{user.email},
    #{user.gender},#{user.registerTime})
  </foreach>
</insert>
```

关键：`useGeneratedKeys="true"`, ` keyProperty="id"`

