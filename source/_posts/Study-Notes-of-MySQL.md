---
title: Study Notes of MySQL
description: 最流行的开源数据库服务器
categories: 
 - Notes
 - MySQL
tags:
 - MySQL
date: 2018-06-09 15:10:40
---

![](https://upload.wikimedia.org/wikipedia/zh/thumb/6/62/MySQL.svg/1200px-MySQL.svg.png)

<!-- more -->

## 注释

单行注释：--

多行注释：/* */

## 主键

**修改自增 ID 起始值**

```mysql
alter table tablename AUTO_INCREMENT=1;
```

## 优化

1. `IN`运算符不要嵌套使用，效率会急剧下降。因此一般使用连接查询，而不使用子查询。

   执行子查询时，MYSQL 需要创建临时表，查询完毕后再删除这些临时表，所以，子查询的速度慢。

## 函数

### 日期

#### DATE_FORMAT

> [MySQL DATE_FORMAT() 函数](http://www.w3school.com.cn/sql/func_date_format.asp)
>
> [DATE_FORMAT(date,format)](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_date-format)

DATE_FORMAT() 函数用于以不同的格式显示日期/时间数据。

eg: 输出格式为：YYYY-mm-dd HH:ss

```sql
select 
DATE_FORMAT(startTime,"%Y-%m-%d %H:%i") AS startTime,
DATE_FORMAT(endTime,"%Y-%m-%d %H:%i") AS endTime
from tableName;
```

| 格式 | 描述                                           |
| ---- | ---------------------------------------------- |
| %a   | 缩写星期名                                     |
| %b   | 缩写月名                                       |
| %c   | 月，数值                                       |
| %D   | 带有英文前缀的月中的天                         |
| %d   | 月的天，数值(00-31)                            |
| %e   | 月的天，数值(0-31)                             |
| %f   | 微秒                                           |
| %H   | 小时 (00-23)                                   |
| %h   | 小时 (01-12)                                   |
| %I   | 小时 (01-12)                                   |
| %i   | 分钟，数值(00-59)                              |
| %j   | 年的天 (001-366)                               |
| %k   | 小时 (0-23)                                    |
| %l   | 小时 (1-12)                                    |
| %M   | 月名                                           |
| %m   | 月，数值(00-12)                                |
| %p   | AM 或 PM                                       |
| %r   | 时间，12-小时（hh:mm:ss AM 或 PM）             |
| %S   | 秒(00-59)                                      |
| %s   | 秒(00-59)                                      |
| %T   | 时间, 24-小时 (hh:mm:ss)                       |
| %U   | 周 (00-53) 星期日是一周的第一天                |
| %u   | 周 (00-53) 星期一是一周的第一天                |
| %V   | 周 (01-53) 星期日是一周的第一天，与 %X 使用    |
| %v   | 周 (01-53) 星期一是一周的第一天，与 %x 使用    |
| %W   | 星期名                                         |
| %w   | 周的天 （0=星期日, 6=星期六）                  |
| %X   | 年，其中的星期日是周的第一天，4 位，与 %V 使用 |
| %x   | 年，其中的星期一是周的第一天，4 位，与 %v 使用 |
| %Y   | 年，4 位                                       |
| %y   | 年，2 位                                       |

### 字符串

#### CONCAT

> [SQL CONCAT() 函数](https://www.w3cschool.cn/sql/sz8w1ozt.html)
>
> [CONCAT(str1,str2,...)](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_concat)

**CONCAT** 函数用于将两个字符串连接为一个字符串

eg: 

```sql
SQL> SELECT CONCAT('FIRST ', 'SECOND');
+----------------------------+
| CONCAT('FIRST ', 'SECOND') |
+----------------------------+
| FIRST SECOND               |
+----------------------------+
1 row in set (0.00 sec)
```

在 MyBatis 中常用于编写模糊查询语句：

```xml
<select id="list" resultType="map">
    select * from tableName
    <if test="condition != null and condition != ''">
        where fieldName like CONCAT('%',#{condition},'%')
    </if>
</select>
```

## 注意

- MySql 中`select null + 1`的结果是`null`