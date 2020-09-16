---
title: SQL Functions in ORACLE
description: Oracle 函数
date: 2019-08-14 13:52:27
categories: 
- Database
- Oracle
tags: 
- Oracle
- SQL-Function
---

<img src="https://regmedia.co.uk/2017/05/03/oracle_balloons2_photo_via_shutterstock.jpg" width="100%"/>

<!-- more -->

> [SQL Functions - Oracle Help Center](https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions001.htm)

## 通用函数

### NVL

格式：**NVL(expr1,expr2)**

功能：如果第一个参数为空那么显示第二个参数的值，如果第一个参数的值不为空，则显示第一个参数本来的值。

实例：

```sql
select ename,NVL(comm, -1) from emp;
------------------------------------

ENAME NVL(COMM,-1)
------- ----
SMITH -1
ALLEN 300
WARD 500
JONES -1
MARTIN 1400
BLAKE -1
FORD -1
MILLER -1
```

#### 空值 NULL 和数字相加

```sql
UPDATE GLOBAL_LOCATION SET POPULATION = NVL(POPULATION, 0) + 1000 WHERE ID = 5022
```

### NVL2

语法

```
NVL2(expr1, expr2, expr3)
```

如果 expr1 为 NULL 返回 expr2，否则返回 expr3。

## 字符函数

### SUBSTR

substr(字符串,截取开始位置,截取长度) //返回截取的字符串

substr('Hello World',2,4) //返回结果为 'ello'

substr('Hello World',-3,3)//返回结果为 'rld' 负数(-i)表示截取的开始位置为字符串右端向左数第i个字符

### LENGTH

length(字符串) //返回字符串长度

### REGEXP_SUBSTR

> [oracle如何拆分以逗号分隔的字符串为多行](https://blog.csdn.net/sofeien/article/details/80534557)
>
> [oracle connect by用法篇](https://blog.csdn.net/wang_yunj/article/details/51040029)

REGEXP_SUBSTR(srcstr, pattern, position, occurrence, modifier)

- srcstr：需要进行正则处理的字符串
- pattern：进行匹配的正则表达式
- position：起始位置，从第几个字符开始正则表达式匹配（默认为1）
- occurrence：标识提取第几个匹配值，默认为1
- modifier：模式（'i'不区分大小写进行检索；'c'区分大小写进行检索。默认为'c'。）

```sql
select regexp_substr('1,7,250','[^,]+',1,1) result from dual;
--- rsult: 1
select regexp_substr('1,7,250','[^,]+',1,2) result from dual;
--- rsult: 7
select regexp_substr('1,7,250','[^,]+',1,3) result from dual;
--- rsult: 250

select regexp_substr('1,7,250','[^,]+',1,rownum) result from dual connect by rownum <= regexp_count('1,7,250', '[^,]+');
--- rsult: 
--- 1
--- 7
--- 250
```

### LISTAGG

> [listagg - Oracle Docs](https://docs.oracle.com/cd/E11882_01/server.112/e41084/functions089.htm)

LISTAGG(measure_expr [, 'delimiter'])  WITHIN GROUP (order_by_clause) [OVER query_partition_clause]

在每个分组内，LISTAGG 根据 order by 子句对列进行排序，然后将排序后的结果拼接起来

- measure_expr：任何基于列的表达式
- delimiter：分隔符
- order_by_clause：order by 子句决定了列值被拼接的顺序

```sql
SELECT
	LISTAGG ( ENGLISH_NAME, ' > ' ) WITHIN GROUP ( ORDER BY ID ) address 
FROM
	GLOBAL_LOCATION 
WHERE
	ID IN ( 1, 7, 250 )
  
--- result: 
--- Asia > China > Shanxi
```

### LPAD

> **pad**
>
> ( padding, padded, pads )
>
> 英 [pæd]美 [pæd]
>
> - n. 衬垫；护具；便笺簿；填补
> - vi. 步行；放轻脚步走
> - vt. **填补**；走
> - n. (Pad)人名；(英)帕德(男子教名 Patrick 的昵称)
>
> [Oracle / PLSQL: LPAD Function - TechOnTheNet](https://www.techonthenet.com/oracle/functions/lpad.php)

LPAD 函数用一组特定的字符填充字符串的左侧。

#### 语法

```
LPAD( string1, padded_length [, pad_string] )
```

- string1：被填充的字符串，当 string1 为 NULL 或者空字符串时，LPAD 函数失效。
- padded_length：填充后返回的结果字符串的长度，当长度小于原来字符串的长度时进行截取。
- pad_string：要填充的字符串，默认为空格。

#### 使用

一般在具有层级关系的查询中，根据层级，在某个字段前会拼接相应数量的空格字符，以便更具有可读性。

比如在查看执行计划时使用如下语句：

```sql
SELECT
	lpad(' ', 5 * ( LEVEL - 1 )) || operation operation,
	options,
	object_name,
	cost,
	position 
FROM
	plan_table START WITH id = 0 CONNECT BY PRIOR id = parent_id;
```

### RPAD

对比参考 LPAD，RPAD是在右侧填充。

## 日期处理函数

### ADD_MONTHS

> [ADD_MONTHS | Oracle](https://docs.oracle.com/cd/B28359_01/server.111/b28286/functions004.htm)
>
> `ADD_MONTHS` returns the date `date` plus `integer` months. A month is defined by the session parameter `NLS_CALENDAR`. The date argument can be a datetime value or any value that can be implicitly converted to `DATE`. The `integer` argument can be an integer or any value that can be implicitly converted to an integer. The return type is always `DATE`, regardless of the datatype of `date`. If `date` is the last day of the month or if the resulting month has fewer days than the day component of `date`, then the result is the last day of the resulting month. Otherwise, the result has the same day component as `date`.

语法：ADD_MONTHS(date, integer)

eg：

```sql
SELECT TO_CHAR( ADD_MONTHS( SYSDATE, - 1 ), 'YYYY-MM-dd' ) "Last month", TO_CHAR( SYSDATE, 'yyyy-MM-dd' ) "Now", TO_CHAR( ADD_MONTHS( SYSDATE, 1 ), 'YYYY-MM-dd' ) "Next month" FROM dual;


"Last month" "Now"	      "Next month"
------------ ------------ ------------
"2019-07-14" "2019-08-14"	"2019-09-14"
```

### LAST_DAY

> [LAST_DAY | Oracle](https://docs.oracle.com/database/121/SQLRF/functions096.htm)
>
> `LAST_DAY` returns the date of the last day of the month that contains `date`. The last day of the month is defined by the session parameter `NLS_CALENDAR`. The return type is always `DATE`, regardless of the data type of `date`.

语法：LAST_DAY(date)

eg：

```sql
SELECT SYSDATE, LAST_DAY( SYSDATE ) "Last", LAST_DAY( SYSDATE ) - SYSDATE "Days Left" FROM DUAL;

"SYSDATE"             "Last"                "Days Left"
--------------------- --------------------- -----------
"2019-08-14 05:41:01" "2019-08-31 05:41:01"          17
```



## 聚合函数

### COUNT

#### 按日期统计

需求：统计收集数量

![pic](https://i.loli.net/2019/08/14/wImuRzxqEybkW5C.png)

##### 按年统计

```sql
SELECT TO_CHAR(COLLECT_DATE, 'yyyy') AS COLLECT_DATE, COUNT(*) AS COUNT FROM COLLECT_RECORD GROUP BY TO_CHAR(COLLECT_DATE, 'yyyy')
```

##### 按月统计

```sql
SELECT TO_CHAR(COLLECT_DATE, 'yyyy-MM') AS COLLECT_DATE, COUNT(*) AS COUNT FROM COLLECT_RECORD GROUP BY TO_CHAR(COLLECT_DATE, 'yyyy-MM')
```

##### 按日统计

```sql
SELECT TO_CHAR(COLLECT_DATE, 'yyyy-MM-dd') AS COLLECT_DATE, COUNT(*) AS COUNT FROM COLLECT_RECORD GROUP BY TO_CHAR(COLLECT_DATE, 'yyyy-MM-dd')
```

## TRUNC

> [Documentation | Oracle](https://docs.oracle.com/cd/B10501_01/appdev.920/a96583/cci08r14.htm#1072049)
> This method returns the Number object truncated at the number of decimal places provided by the parameter specified.

语法：TRUNC(number, num_digits)

- number：需要截断取整的数字
- num_digits：用于指定取整精度的数字，默认值为 0

eg：

- 数字

  ```sql
  select trunc(123.458) from dual    --123
  select trunc(123.458,0) from dual  --123
  select trunc(123.458,1) from dual  --123.4
  select trunc(123.458,-1) from dual --120
  select trunc(123.458,-4) from dual --0
  select trunc(123.458,4) from dual  --123.458
  select trunc(123) from dual        --123
  select trunc(123,1) from dual      --123
  select trunc(123,-1) from dual     --120
  ```

- 日期

  ```sql
  select trunc(sysdate) from dual
  --2019-08-14 00:00:00  今天的日期
  
  select trunc(sysdate, 'mm') from dual
  --2019-08-01 00:00:00  当月第一天
  
  select trunc(sysdate,'yy') from dual
  --2019-01-01 00:00:00  当年第一天
  
  select trunc(sysdate,'dd') from dual
  --2019-08-14 00:00:00  当前年月日
  
  select trunc(sysdate,'yyyy') from dual
  --2019-01-01 00:00:00  当年第一天
  
  select trunc(sysdate,'d') from dual
  --2019-08-11 00:00:00  当前星期的第一天(星期天)
  
  select trunc(sysdate, 'hh') from dual
  --2019-08-14 03:00:00  当前时间的第一分钟
  
  select trunc(sysdate, 'mi') from dual
  --2019-08-14 03:56:00  当前时间的第一秒，TRUNC()函数没有秒的精确
  ```

## JSON

### JSON_TABLE

> [json_table - Oracle Docs](https://docs.oracle.com/database/121/SQLRF/functions092.htm)
>
> 

语法：

```
JSON_TABLE
  ( expr [ FORMAT JSON ], JSON_path_expression
    [ JSON_table_on_error_clause ] JSON_columns_clause )
```

eg：

```sql
CREATE TABLE j_purchaseorder
  (id RAW (16) NOT NULL,
   date_loaded TIMESTAMP(6) WITH TIME ZONE,
   po_document CLOB CONSTRAINT ensure_json CHECK (po_document IS JSON));
```

```sql
INSERT INTO j_purchaseorder
  VALUES (
    SYS_GUID(),
    SYSTIMESTAMP,
    '{"PONumber"              : 1600,
      "Reference"             : "ABULL-20140421",
       "Requestor"            : "Alexis Bull",
       "User"                 : "ABULL",
       "CostCenter"           : "A50",
       "ShippingInstructions" : {"name"   : "Alexis Bull",
                                 "Address": {"street"   : "200 Sporting Green",
                                              "city"    : "South San Francisco",
                                              "state"   : "CA",
                                              "zipCode" : 99236,
                                              "country" : "United States of America"},
                                 "Phone" : [{"type" : "Office", "number" : "909-555-7307"},
                                            {"type" : "Mobile", "number" : "415-555-1234"}]},
       "Special Instructions" : null,
       "AllowPartialShipment" : true,
       "LineItems" : [{"ItemNumber" : 1,
                       "Part" : {"Description" : "One Magic Christmas",
                                 "UnitPrice"   : 19.95,
                                 "UPCCode"     : 13131092899},
                       "Quantity" : 9.0},
                      {"ItemNumber" : 2,
                       "Part" : {"Description" : "Lethal Weapon",
                                 "UnitPrice"   : 19.95,
                                 "UPCCode"     : 85391628927},
                       "Quantity" : 5.0}]}');
```

```sql
SELECT jt.*
FROM j_purchaseorder,
JSON_TABLE(po_document, '$.ShippingInstructions.Phone[*]'
COLUMNS (row_number FOR ORDINALITY,
         phone_type VARCHAR2(10) PATH '$.type',
         phone_num VARCHAR2(20) PATH '$.number'))
AS jt;

ROW_NUMBER PHONE_TYPE PHONE_NUM
---------- ---------- --------------------
         1 Office     909-555-7307
         2 Mobile     415-555-1234
```

## 分析函数

### ROW_NUMBER

> [row_number - Oracle Docs](https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions137.htm)

语法：

```
ROW_NUMBER( )
   OVER ([ query_partition_clause ] order_by_clause)
```

eg：查找最新一条收集任务

```sql
SELECT
	* 
FROM
	( SELECT ct.* , row_number () over ( ORDER BY ct.CREATE_DATE DESC ) rn FROM COLLECT_TASK ct ) 
WHERE
	rn = 1
```

```
ID 	COLLECTOR_ID CITY_ID CREATE_BY CREATE_DATE 	      UPDATE_BY UPDATE_DATE 	     DEL_FLAG RN 
--- ------------ ------- --------- ------------------ --------- ------------------ -------- --
135 176 	       5185 	 1 	       26/8/2019 17:23:37 1 	      26/8/2019 17:25:14 0 	      1 
```

