---
title: Study Notes of Oracle
description: Oracle 笔记
date: 2019-08-07 09:38:29
categories: Oracle
tags: Oracle
---

<img src="https://www.datocms-assets.com/2885/1522106007-oracle.png" width="100%"/>

<!-- more -->

## 命令

### 查看版本

```sql
SELECT version FROM v$instance;
```

## [函数](https://libo9527.github.io/2019/08/14/SQL-Functions-in-ORACLE/)

## 批量插入

```sql
INSERT INTO USER ( ID, USERNAME, PASSWORD, CREATE_DATE, DEL_FLAG ) 
  SELECT SEQ_USER.nextval, t.* 
  FROM
    (
    SELECT 'zhangsan' USERNAME, 'zxode23' PASSWORD, SYSDATE CREATE_DATE, 0 DEL_FLAG FROM dual 
    UNION ALL
    SELECT 'lisi' USERNAME, '9fhd2fd' PASSWORD, SYSDATE CREATE_DATE, 0 DEL_FLAG FROM dual 
    ) t
```

MyBatis 批量插入，使用序列做自增ID

```xml
<insert id="insertList">
  INSERT INTO USER ( ID, USERNAME, PASSWORD, CREATE_DATE, DEL_FLAG )
  SELECT SEQ_USER.nextval, t.* FROM
  <foreach collection="list" item="item" open="(" separator="UNION ALL" close=")">
    SELECT #{item.USERNAME} USERNAME, #{item.PASSWORD} PASSWORD, SYSDATE CREATE_DATE, 0 DEL_FLAG FROM dual
  </foreach>
  t
</insert>
```

union all：不去重

union：去重

## GROUP BY

### 缺省值 NULL

- Oracle 在 Order by 时认为 null 是最大值，所以如果是 ASC 升序则排在最后，DESC 降序则排在最前。

- 可以使用 nulls first 或者 nulls last 语法来控制 NULL 的位置。

  ```sql
  ---将 null 始终放在最前
  SELECT * FROM TABLE ORDER BY COLUMN nulls first
  ---将 null 始终放在最后
  SELECT * FROM TABLE ORDER BY COLUMN nulls last
  ```

## EXISTS

> [EXISTS Condition | Oracle](https://docs.oracle.com/cd/B19306_01/server.102/b14200/conditions012.htm)
>
> An `EXISTS` condition tests for existence of rows in a subquery.

一般情况下公司数据库不会加外健来关联两张表，查询时为了避免数据不存在会使用 EXISTS 进行过滤。

```sql
SELECT
	d.department_id 
FROM
	departments d 
WHERE
EXISTS ( 
	SELECT
		* 
	FROM
		employees e 
	WHERE
	d.department_id = e.department_id
)
```

## GROUP BY VS PARTITION BY

> Oracle 对于 GROUP BY 是严格的，oracle 中规定，使用 group by 时，select 后面所有不是聚合函数的字段，都必须出现在 group by 后面，否则会报错：“ORA-00979: not a GROUP BY expression”。而 MySQL 则不同，如果 SELECT 出来的字段在 GROUP BY 后面没有出现，那么会随机取出一个值。

> [partition by and group by | Oracle](https://asktom.oracle.com/pls/asktom/asktom.search?tag=partition-by-and-group-by)
>
> Totally different things.
>
> GROUP BY is about aggregation. Take 'n' rows and reduce the number of rows (by summing, or max, or min etc)..But we are *consolidating* some data.
>
> PARTITION BY is about carving up data into chunks. Take 'n' rows, apply some rule to split the rows into buckets...but will still have 'n' rows.

- GROUP BY 是关于聚合函数的，PARTITION BY 是敢于分析函数的。
- 对于 n 行数据，GROUP BY 返回的肯定小于等于 n 行，而 PARTITION BY 会返回 n 行。

eg：求各部门员工工资总和

```sql
-- 使用分组函数GROUP BY方式
SELECT deptno 部门编号,SUM(sal) 部门工资总和 FROM emp GROUP BY deptno;
-- 结果
-----------------------
| 部门编号 | 部门工资总和 |
| 30 | 10900 |
| 20 | 10575 |
| 10 | 9250 |
```

```sql
-- 使用分析函数 PARTITION BY方式
SELECT deptno 部门编号,SUM(SAL) OVER (partition by sal) 部门工资总和 FROM emp;
-----------------
| 部门编号 | 部门工资总和 |
|	30 | 1450 |
|	30 | 1500 |
|	30 | 3200 |
|	20 | 3200 |
|	30 | 3500 |
|	30 | 3500 |
|	10 | 1800 |
|	10 | 2450 |
|	30 | 2850 |
|	20 | 2975 |
|	20 | 6000 |
|	20 | 6000 |
|	10 | 5000 |
-----------------
SELECT deptno 部门编号 ,ename 员工姓名, sal 员工工资, SUM(sal) OVER(PARTITION BY deptno ORDER BY sal) 所在部门工资总和  FROM emp;
----------------------------------------------
| 部门编号 | 员工姓名 | 员工工资 | 所在部门工资总和 |
| 10 | MILLER | 1800 | 1800 |
| 10 | CLARK | 2450 | 4250 |
| 10 | KING | 5000 | 9250 |
| 20 | ADAMS | 1600 | 1600 |
| 20 | JONES | 2975 | 4575 |
| 20 | SCOTT | 3000 | 10575 |
| 20 | FORD | 3000 | 10575 |
| 30 | JAMES | 1450 | 1450 |
| 30 | TURNER | 1500 | 2950 |
| 30 | ALLEN | 1600 | 4550 |
| 30 | WARD | 1750 | 8050 |
| 30 | MARTIN | 1750 | 8050 |
| 30 | BLAKE | 2850 | 10900 |
```

## 递归查询

语法：

```sql
SELECT ... FROM tablename WHERE ... START WITH ... CONNECT BY PRIOR ...
```

eg：

地址表 GLOBAL_LOCATION

![pic](https://i.loli.net/2019/08/13/6nlYZxW5zsLbfAC.png)

- 查询北京所有的子孙节点

  ```sql
  SELECT ID, PARENT_ID, NAME FROM GLOBAL_LOCATION START WITH PARENT_ID = 247 CONNECT BY PRIOR ID = PARENT_ID
  ```

- 查询海淀所有的祖先节点

  ```sql
  SELECT ID, PARENT_ID, NAME FROM GLOBAL_LOCATION START WITH ID = 3026 CONNECT BY PRIOR PARENT_ID = ID
  ```

  

## Comprehensive examples in work

### 地址树远程搜索

需求说明：input 框中输入地址，远程搜索出匹配节点，由于同名地址到存在，要求将完整地址一同显示。

![pic](https://i.loli.net/2019/08/10/TCvoScY2A7Vajd8.png)

表结构：

```sql
CREATE TABLE "SKYSTAR"."GLOBAL_LOCATION_copy1" (
  "ID" NUMBER(20) VISIBLE NOT NULL ,
  "PARENT_ID" NUMBER(20) VISIBLE ,
  "PATH" VARCHAR2(100 BYTE) VISIBLE ,
  "ENGLISH_NAME" VARCHAR2(255 BYTE) VISIBLE
)
```

样例数据：

```sql
INSERT INTO "GLOBAL_LOCATION"("ID", "PARENT_ID", "PATH", "ENGLISH_NAME") VALUES ('250', '7', ',1,7,250,', 'Shanxi');
```

1. 根据输入查找节点

  ```sql
  SELECT ID, ENGLISH_NAME, PATH FROM GLOBAL_LOCATION WHERE UPPER(ENGLISH_NAME) LIKE '%' || UPPER(#{NAME}) || '%'
  ```

2. 去掉 PATH 前后的 `,`

   ```sql
   SELECT SUBSTR( ',1,7,250,', 2, LENGTH( ',1,7,250,' ) - 2 ) FROM dual
   ```

3. 将去掉前后 `,` 的路径 PATH 按 `,` 分割得到祖先 ID

   ```sql
   SELECT
   	REGEXP_SUBSTR( '1,7,250', '[^,]+', 1, ROWNUM ) 
   FROM
   	dual CONNECT BY ROWNUM <= LENGTH( '1,7,250' ) - LENGTH(REGEXP_REPLACE( '1,7,250', ',', '' )) + 1
   ```

4. 查找所有祖先节点，并使用 `>` 连接

   ```sql
   SELECT
   	LISTAGG ( ENGLISH_NAME, ' > ' ) WITHIN GROUP ( ORDER BY ID ) address 
   FROM
   	( SELECT ID, ENGLISH_NAME FROM GLOBAL_LOCATION WHERE ID IN ( 1, 7, 250 ) )
   ```

汇总后的 SQL：

```sql
SELECT
    ID,
    ENGLISH_NAME,
(
    SELECT
        LISTAGG ( ENGLISH_NAME, ' > ' ) WITHIN GROUP ( ORDER BY ID ) ADDRESS
    FROM
    (
        SELECT
            ID,
            ENGLISH_NAME
        FROM
            GLOBAL_LOCATION
        WHERE ID IN (
            SELECT
                REGEXP_SUBSTR( t1.ADDRESS, '[^,]+', 1, ROWNUM )
            FROM
                dual CONNECT BY ROWNUM <= LENGTH( t1.address ) - LENGTH(REGEXP_REPLACE( t1.ADDRESS, ',', '' )) + 1)
    )
) FULL_ADDRESS
FROM
(
    SELECT
        ID,
        ENGLISH_NAME,
        ( SELECT SUBSTR( gl.PATH, 2, LENGTH( gl.PATH ) - 2 ) FROM dual ) ADDRESS
    FROM
    ( SELECT ID, ENGLISH_NAME, PATH FROM GLOBAL_LOCATION WHERE UPPER( ENGLISH_NAME ) LIKE '%' || UPPER( 'shan' ) || '%' ) gl
) t1
```

![pic](https://i.loli.net/2019/08/10/F6XYRiD7Q29OHrE.png)

![pic](https://i.loli.net/2019/08/10/NL9lIHtJnXxmC38.png)

## {% post_link Using-Triggers-in-ORACLE 触发器（TRIGGER） %}

## ERROR

### ORA-03113

> [Oracle错误——ORA-03113:通信通道的文件结尾 解决办法](https://blog.csdn.net/zwk626542417/article/details/39667999)

#### 问题描述

Oracle 中使用 `COUNT(DISTINCT xxx)` 时出现：

```sql
ORA-03113：end-of-file on communication  channel
```

而且只有在原始表中执行会报这个错，将原始表复制后再执行又不报错，或者把 `DISTINCT` 去掉再执行也不报错。

#### SQL

```sql
SELECT
    t.address_cityTown AS ID,
    gl.PATH,
    COUNT( DISTINCT t.id ) AS collectedQTY
FROM
    (
SELECT
    cr.id,
    jt.*
FROM
    ( SELECT * FROM CUSTOMER_RAW WHERE DEL_FLAG = 0 ) cr,
    JSON_TABLE (
    DATA,
    '$.generalInfo.address[*]' COLUMNS ( row_number FOR ORDINALITY, address_cityTown NUMBER ( 20 ) PATH '$.cityTown' )) AS jt
    ) t
    LEFT JOIN GLOBAL_LOCATION gl ON t.address_cityTown = gl.ID
GROUP BY
    t.address_cityTown,
    gl.PATH
```

#### TABLE

> [CUSTOMER_RAW.sql](/download/CUSTOMER_RAW.sql)

### ORA-04098

> [Oracle触发器，解决ORA-04098: 触发器 'USER.DECTUSERTEST_TRI' 无效且未通过重新验证](https://www.cnblogs.com/enternal07/p/6762752.html)

#### 问题描述

创建了一个触发器，在执行触发事件的 SQL 时报错：

```sql
ORA-04098: trigger 'SKYSTAR.COUNT_COLLECTED_QTY' is invalid and failed re-validation
```

#### 触发器

```sql
CREATE OR REPLACE TRIGGER Count_Collected_Qty 
AFTER INSERT  
ON CUSTOMER_RAW_TEST
FOR each ROW
BEGIN
		UPDATE GLOBAL_LOCATION_TEST SET COLLECTED_QTY = COLLECTED_QTY + 1 
END;
```

#### 触发 SQL

```sql
INSERT INTO "CUSTOMER_RAW_TEST"("ID", "DATA", "SOURCE_TYPE_ID", "STATUS", "CREATE_BY", "CREATE_DATE", "UPDATE_BY", "UPDATE_DATE", "DEL_FLAG", "GOOGLE_FLAG", "GOOGLE_ADDRESS", "PROCESS_FLAG") VALUES 
('5', '{"generalInfo":{"address":[{"country":5100,"countryName":"Test Country A","state":5110,"stateName":"Test State AA","cityTown":5111,"cityTownName":"Test City AAA"}]}}', '1', '213', '1435', TO_DATE('2019-09-16 15:36:37', 'SYYYY-MM-DD HH24:MI:SS'), '1435', TO_DATE('2019-09-16 15:36:37', 'SYYYY-MM-DD HH24:MI:SS'), '0', NULL, NULL, NULL);
```

#### TABLE

[GLOBAL_LOCATION_TEST.sql](/download/GLOBAL_LOCATION_TEST.sql)

[CUSTOMER_RAW_TEST.sql](/download/CUSTOMER_RAW_TEST.sql)

#### 原因

触发操作中的 SQL 语句没有加 `;`

