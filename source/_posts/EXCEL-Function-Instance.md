---
title: EXCEL Function Instance
description: Excel 函数实例
date: 2019-11-01 09:04:59
categories: Excel
tags: Excel
---

<img src="https://bitcoinist.com/wp-content/uploads/2019/05/shutterstock_1067559386.png" width="100%"/>

<!-- more -->

## SUMIF

> [SUMIF 函数- Office 支持 - Office Support - Office 365](https://support.office.com/zh-cn/article/sumif-函數-169b8c99-c05c-4483-a712-1697a653039b)
>
> **单条件求和**函数，可对范围中符合指定条件的值求和。

### 语法

```
SUMIF(range, criteria, [sum_range])
```

- range：要按条件筛选的单元格区域
- criteria：筛选条件
- sum_range：要求和的实际单元格区域

### 实例

#### Sheet1

| 日期       | 时间  | 名称           | 金额(¥) |
| ---------- | ----- | -------------- | ------- |
| 2019/10/21 | 8:15  | 地铁           | 3       |
| 2019/10/21 | 12:42 | 卤肉面+油条    | 16      |
| 2019/10/21 | 18:46 | 哈密瓜         | 26.8    |
| 2019/10/21 | 18:59 | 桂圆           | 13.8    |
| 2019/10/21 | 20:17 | 地铁           | 3       |
| 2019/10/21 | 22:26 | 谷歌助手       | 15.99   |
| 2019/10/22 | 9:06  | 地铁           | 3       |
| 2019/10/22 | 12:55 | 西红柿鸡蛋拌面 | 16      |
| 2019/10/22 | 17:10 | 地铁           | 3       |
| 2019/10/23 | 8:27  | 借给吕江       | 1000    |
| 2019/10/23 | 8:48  | 地铁           | 3       |
| 2019/10/23 | 12:42 | 肉酱拌面+加面  | 18.5    |
| 2019/10/23 | 17:56 | 地铁           | 3       |

#### Sheet2

| 日期       | 总计   |
| ---------- | ------ |
| 2019/10/21 | 78.59  |
| 2019/10/22 | 22     |
| 2019/10/23 | 1024.5 |

#### 使用

| 位置      | 公式                                                | 说明                                                   | 含义                            |
| --------- | --------------------------------------------------- | ------------------------------------------------------ | ------------------------------- |
| Sheet2 B2 | =SUMIF(Sheet1!A2:A14,<br />Sheet2!A2,Sheet1!D2:D14) | Sheet1  A2 ～ A14 中日期等于Sheet2中 A2 日期的金额总和 | Sheet1 中 2019/10/21 的金额总和 |
| Sheet2 B3 | =SUMIF(Sheet1!A2:A14,<br />Sheet2!A3,Sheet1!D2:D14) | Sheet1  A2 ～ A14 中日期等于Sheet2中 A3 日期的金额总和 | Sheet1 中 2019/10/22 的金额总和 |
| Sheet2 B4 | =SUMIF(Sheet1!A2:A14,<br />Sheet2!A4,Sheet1!D2:D14) | Sheet1  A2 ～ A14 中日期等于Sheet2中 A4 日期的金额总和 | Sheet1 中 2019/10/23 的金额总和 |

## SUMIFS

> [SUMIFS 函数- Office 支持 - Office Support - Office 365](https://support.office.com/zh-cn/article/sumifs-函数-c9e748f5-7ea7-455d-9406-611cebce642b)
>
> **多条件求和**函数，可对范围中符合指定条件的值求和。

### 语法

```
SUMIFS(sum_range, criteria_range1, criteria1, [criteria_range2, criteria2], ...)
```

- sum_range：要求和的单元格区域
- criteria_range1：条件 1 的筛选区域
- criteria：条件 1 的筛选条件

### 实例

#### Sheet1

> 同 SUMIF Sheet1

#### Sheet2

| 日期       | 12:00 | 13:00 |
| ---------- | ----- | ----- |
| 2019/10/21 | 16    | 0     |
| 2019/10/22 | 16    | 0     |
| 2019/10/23 | 18.5  | 0     |

#### 使用



<style>
table th:first-of-type {  #first表示表格第一列
    width: 100px; # 可使用%比例
}
</style>

| 位置 | 公式                                                         | 说明                                                         | 含义                                          |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| Sheet2 B1 | =SUMIFS(Sheet1!E2:E54,<br />Sheet1!A2:A54, Sheet2!A2,<br />Sheet1!B2:B54,">="&Sheet2!B1, <br />Sheet1!B2:B54,"<"&Sheet2!C1) | Sheet1 A2~A54 中日期等于 Sheet2 B1 的日期，<br />并且 Sheet1 B2 ~ B54 中时间大于等于 Sheet2 B1 <br />的时间小于 C1 的时间的金额总和 | Sheet1 中 2019/10/22 12:00 ~ 13:00 的金额总和 |
