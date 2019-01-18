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



[PAT——甲级1042：Shuffling Mashine](http://www.morecoder.com/article/1115597.html)



段错误：

段错误是访问了不能访问的内存，

大概率数组越界



long long 类型在 C++(g++) 下错误 C++(clang++) 下正确。

```c++ PAT-1065.cpp
#include <cstdio>

int main(){
  int t;
  scanf("%d", &t);
  for (int i = 0; i < t; i++){
    long long a, b, c;
    scanf("%lld%lld%lld", &a, &b, &c);
    if(a > 0 && b > 0 && a + b < 0){
      printf("Case #%d: true\n", i+1);
    }else if(a < 0 && b < 0 && a + b >= 0){
      printf("Case #%d: false\n", i+1);
    }else if(a + b > c){
      printf("Case #%d: true\n", i+1);
    }else {
      printf("Case #%d: false\n", i+1);
    }
  }
  return 0;
}
```



注意计数器自增自减和条件判断的前后顺序。

```c++
count--;
if(count > 0){
    ...
}
////
if(count > 0){
    ...
}
count--;
```

