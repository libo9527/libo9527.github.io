---
title: Using Triggers in ORACLE
description: ORACLE 中触发器的使用
date: 2019-09-17 15:03:58
categories:
 - Oracle
 - Trigger
tags: Trigger
---

<img src="https://i.ytimg.com/vi/R3fvX_xf5P4/maxresdefault.jpg" width="100%"/>

<!-- more -->

## 触发器

> [Using Triggers | ORACLE](https://docs.oracle.com/database/121/TDDDG/tdddg_triggers.htm#TDDDG50000)
>
> A **trigger** is a PL/SQL unit that is stored in the database and (if it is in the enabled state) automatically executes ("fires") in response to a specified event.
>
> [ORACLE PL/SQL编程之八：把触发器说透](https://www.cnblogs.com/huyong/archive/2011/04/27/2030466.html)

触发器在数据库里以独立的对象存储，它与存储过程和函数不同的是，存储过程与函数需要用户显示调用才执行，而触发器是由一个事件来启动运行。即触发器是当某个事件发生时自动地隐式运行。并且，触发器不能接收参数。所以运行触发器就叫触发或点火（firing）。

ORACLE 事件指的是对数据库的表或视图进行的 INSERT、UPDATE 及 DELETE 操作。ORACLE将触发器的功能扩展到了触发 ORACLE，如数据库的启动与关闭等。

所以触发器常用来完成由数据库的完整性约束难以完成的复杂业务规则的约束，或用来监视对数据库的各种操作，实现审计的功能。 

### 组成

- 触发事件

  引起触发器被触发的事件。 例如：DML语句(INSERT, UPDATE, DELETE语句对表或视图执行数据处理操作)、DDL语句（如CREATE、ALTER、DROP语句在数据库中创建、修改、删除模式对象）、数据库系统事件（如系统启动或退出、异常错误）、用户事件（如登录或退出数据库）。

- 触发时间

  即该 TRIGGER 是在触发事件发生之前（BEFORE）还是之后（AFTER）触发，也就是触发事件和该 TRIGGER 的操作顺序。

- 触发操作

  即该 TRIGGER 被触发之后的目的和意图，正是触发器本身要做的事情。 例如：PL/SQL 块。

- 触发对象

  包括表、视图、模式、数据库。只有在这些对象上发生了符合触发条件的触发事件，才会执行触发操作。

- 触发条件

  由 WHEN 子句指定一个逻辑表达式。只有当该表达式的值为 TRUE 时，遇到触发事件才会自动执行触发器，使其执行触发操作。

- 触发频率

  说明触发器内定义的动作被执行的次数。即语句级(STATEMENT)触发器和行级(ROW)触发器。

### 分类

根据**触发频率**分类：

- 语句级(STATEMENT)触发器：

  指当触发事件发生时，该触发器只执行一次；

- 行级(ROW)触发器：

  指当触发事件发生时，对受到该操作影响的每一行数据，触发器都单独执行一次。

根据**触发事件类型**分类：

- DML 触发器

  ORACLE可以在DML语句进行触发，可以在DML操作前或操作后进行触发，并且可以对每个行或语句操作上进行触发。

- 替代触发器

  由于在ORACLE里，不能直接对由两个以上的表建立的视图进行操作。所以给出了替代触发器。它就是ORACLE 8专门为进行视图操作的一种处理方法。 

- 系统触发器

  ORACLE 8i 提供了第三种类型的触发器叫系统触发器。它可以在ORACLE数据库系统的事件中进行触发，如ORACLE系统的启动与关闭等。 

### 创建

> [CREATE TRIGGER | ORACLE](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_7004.htm)

```sql
CREATE [ OR REPLACE ] TRIGGER [ schema. ]trigger
   { BEFORE | AFTER | INSTEAD OF }
   { dml_event_clause
   | { ddl_event [ OR ddl_event ]...
     | database_event [ OR database_event ]...
     }
     ON { [ schema. ]SCHEMA
        | DATABASE
        }
   }
   [ WHEN (condition) ]
   { pl/sql_block | call_procedure_statement } ;
```

eg：

在给 CUSTOMER_RAW_TEST 表插入数据时自动执行 `UPDATE GLOBAL_LOCATION_TEST SET COLLECTED_QTY = COLLECTED_QTY + 1 ;`

```sql
CREATE OR REPLACE TRIGGER Count_Collected_Qty 
AFTER INSERT  
ON CUSTOMER_RAW_TEST
FOR each ROW
BEGIN
		UPDATE GLOBAL_LOCATION_TEST SET COLLECTED_QTY = COLLECTED_QTY + 1 ;
END;
```

#### 注意

- 触发器不接受参数

- 一个表上最多可有12个触发器，但同一时间、同一事件、同一类型的触发器只能有一个。并各触发器之间不能有矛盾。

- 在一个表上的触发器越多，对在该表上的DML操作的性能影响就越大。

- 触发器最大为32KB。若确实需要，可以先建立过程，然后在触发器中用CALL语句进行调用。

- **在触发器的执行部分只能用DML语句（SELECT、INSERT、UPDATE、DELETE），不能使用DDL语句（CREATE、ALTER、DROP）**

- 触发器中不能包含事务控制语句(COMMIT，ROLLBACK，SAVEPOINT)。因为触发器是触发语句的一部分，触发语句被提交、回退时，触发器也被提交、回退了。

- 在触发器主体中调用的任何过程、函数，都不能使用事务控制语句。

- 在触发器主体中不能申明任何 Long 和 blob 变量。新值 new 和旧值 old 也不能向表中的任何long和blob列。

- 不同类型的触发器(如DML触发器、INSTEAD OF触发器、系统触发器)的语法格式和作用有较大区别。 

### 查找

```sql
SELECT * FROM user_triggers;
```

```sql
SELECT object_name FROM user_objects WHERE object_type = 'TRIGGER';
```

```sql
SELECT * FROM all_triggers WHERE table_name = 'CUSTOMER_RAW_TEST';
```

```sql
SELECT * FROM user_source WHERE name = 'COUNT_COLLECTED_QTY';
```

### 删除

```sql
DROP TRIGGER COUNT_COLLECTED_QTY;
```

注：当删除表或视图时，建立在这些对象上的触发器也随之删除。 

### 禁用/启用

```sql
ALTER TIGGER trigger_name [DISABLE | ENABLE ];
```

ALTER TRIGGER 语句只能改变单个触发器的状态，而 ALTER TABLE 语句则能够改变与指定表相关的所有触发器的使用状态。格式为： 

```sql
ALTER TABLE [schema.]table_name {ENABLE|DISABLE} ALL TRIGGERS;
```

### 查看权限

```sql
SELECT owner, object_name, object_type, status FROM dba_objects WHERE object_name = 'COUNT_COLLECTED_QTY';
```

### 使用 Navicat 管理触发器

> [浅谈 Navicat for Oracle 表触发器](http://www.formysql.com/wenti/oracle-chufaqi.html)