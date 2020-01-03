---
title: Week-based-year
description: Java 日期格式化注意事项
date: 2020-01-02 09:38:28
categories: JDK
tags: JDK
---

<div style="width: 100%; height: 796px; background-image: url(https://i.v2ex.co/785QLPbb.jpeg); background-repeat: no-repeat; background-position: 0 -180px"></div>
<!-- more -->

> [你今天因为 YYYY-MM-dd 被提 BUG 了吗](https://v2ex.com/t/633650)
>
> [Serious Security: The decade-ending “Y2K bug” that wasn’t](https://nakedsecurity.sophos.com/2019/12/23/serious-security-the-decade-ending-y2k-bug-that-wasnt/)

## BUG 🐛

正确时间应为 `2019/12/29 02:33`，却错误的显示为 `2020/12/29 02:33`

```java
package com.gzhennaxia.admin.util;

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

/**
 * @author bo li
 * @date 2020-01-02 17:42
 */
public class CarefulWithThatDateEugene {

    private static void tryit(int Y, int M, int D, String pat) {
        DateTimeFormatter fmt = DateTimeFormatter.ofPattern(pat);
        LocalDate         dat = LocalDate.of(Y,M,D);
        String            str = fmt.format(dat);
        System.out.printf("Y=%04d M=%02d D=%02d " +
                "formatted with " +
                "\"%s\" -> %s\n",Y,M,D,pat,str);
    }
    public static void main(String[] args){
        tryit(2019,12,28,"YYYY-MM-dd");
        tryit(2019,12,29,"YYYY-MM-dd");
        tryit(2019,12,30,"YYYY-MM-dd");
        tryit(2019,12,31,"YYYY-MM-dd");
        tryit(2020,01,1,"DD/MM/YYYY");
        tryit(2020,01,2,"YYYY-MM-DD");
        tryit(2020,01,3,"YYYY-MM-DD");
        tryit(2020,01,4,"YYYY-MM-DD");
        tryit(2020,01,5,"YYYY-MM-DD");
    }
}
```

```
Y=2019 M=12 D=28 formatted with "YYYY-MM-dd" -> 2019-12-28
Y=2019 M=12 D=29 formatted with "YYYY-MM-dd" -> 2020-12-29
Y=2019 M=12 D=30 formatted with "YYYY-MM-dd" -> 2020-12-30
Y=2019 M=12 D=31 formatted with "YYYY-MM-dd" -> 2020-12-31
Y=2020 M=01 D=01 formatted with "DD/MM/YYYY" -> 01/01/2020
Y=2020 M=01 D=02 formatted with "YYYY-MM-DD" -> 2020-01-02
Y=2020 M=01 D=03 formatted with "YYYY-MM-DD" -> 2020-01-03
Y=2020 M=01 D=04 formatted with "YYYY-MM-DD" -> 2020-01-04
Y=2020 M=01 D=05 formatted with "YYYY-MM-DD" -> 2020-01-05
```

### 原因

Java 日期格式化时使用的[**模式字符串**](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html#patterns)为 `YYYY/MM/dd HH:ss`！！！应该改为  `yyyy/MM/dd HH:ss`！！！

Java 1.8 中 `YYYY` 表示 `week-based-year`，意思是年份是基于周进行计算的，一年有 52 个周余 1/2 天，`week-based-year` 就是决定这多出来的一天或者两天所在的周应该属于哪一年的。

ISO-8601 规定：
1. 一周从周一到周日，即周一为每周的第一天
2. 年末被拆分的周分配给天数占多数的年份，即哪个年份的天在这一周里居多，那么这一周就算是哪一年。

回归问题，19 年年末 20 年年初的日期与周对应关系为：

| 2019/12/30 | 2019/12/31 | 2020/01/01 | 2020/01/02 | 2020/01/03 | 2020/01/04 | 2020/01/05 |
| ---------- | ---------- | ---------- | ---------- | ---------- | ---------- | ---------- |
| 周一       | 周二       | 周三       | 周四       | 周五       | 周六       | 周天       |

由于在这一周里 19 年有 2 天，20 年有 5 天，所以根据 ISO-8601 的规定，这一周是 2020 年的第一周，所以如果使用 `YYYY` 作为年份的模式字符串，那么转换后得到的年份结果将会是 2020。


### YYYY 的意义

> [Serious Security: The decade-ending “Y2K bug” that wasn’t](https://nakedsecurity.sophos.com/2019/12/23/serious-security-the-decade-ending-y2k-bug-that-wasnt/)
>
> For accounting convenience, therefore, it s conventional to treat some years as having 52 full weeks, and others as having 53 weeks, which keeps things like weekly revenue plans and weekly payroll evened out in the long run.
>
> 因此，为了方便核算，通常将某些年份视为有52个完整的星期，而其他年份则视为有53个星期，这样从长远来看，就可以保持诸如每周收入计划和每周工资的平衡。

### 总结

平常日期格式化时使用的标准模式字符串为 <span style="background-color: rgba(27,31,35,.05);border-radius: 3px;font-size: 85%;margin: 0;padding: .2em 0 .2em .4em;">yyyy/MM/dd HH:</span><span style="background-color:rgba(27,31,35,.05);border-radius: 3px;font-size: 85%;margin: 0;padding: .2em .4em .2em 0;">mm:ss</span> (**小大小大小小**)


