---
title: Prepare for PAT
description: 为PAT做准备
comments: false
hidden: true
date: 2019-01-11 11:46:45
categories:
 - Algorithm
 - PAT
tags:
 - PAT
---

<img src="https://i.loli.net/2019/01/11/5c3813443dce6.jpg" width="100%"/>

<!-- more -->

> [PAT官网](https://www.patest.cn/)

## 计划

### 1/11-2/20

一共 50h

[PAT-timeaxis.xlsx](https://doc.weiyun.com/bec9710ef6076b30f1154413324b9ef1)

![](https://i.loli.net/2019/01/11/5c38115365f11.jpg)

## 代码

> [PAT](https://github.com/libo9527/PAT)

## 总结

1. 注意审题
   - 输入变量名，顺序，格式...
   - 输出格式
     - 时间格式
     - 高位补零







条件判断中如果有对称性的逻辑，尽量适用下面这种方式，即变量不动，逻辑判断动。

```java
if(a1 + b1 == a2 && a1 + b1 != b2){
    a++;
}else if(a1 + b1 != a2 && a1 + b1 == b2){
    b++;
}
```

避免适用下面这种方式（比较混乱）

```java
if(a1 + b1 == a2 && a1 + b1 != b2){
    a++;
}else if(a1 + b1 == b2 && a1 + b1 != a2){
    b++;
}
```

