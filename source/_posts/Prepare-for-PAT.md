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

> [Summary of PAT](https://libo9527.github.io/2019/02/15/Summary-of-PAT/)

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



PAT C++编译不能使用的C函数：

​    1.itoa()    

​    2.gets()

PTA网站如果使用C++(g++)编译不能使用get(),如果使用c(gcc)编译不能使用C++头文件

**PAT的编译器在C++编译环境下不能使用gets(),我们用fgets(char\* a,int maxlens,stdin)替代。(注意，前面需要用getchar()接收一下换行符，因为大部分题目在接收整行数据前，有一个数据个数的输入)**。



用`fgets()`、`fputs()`函数代替`gets()`、`puts()`函数。



未读取到我们要求的大小时（我们要求读的是10但只输入了3个字符），遇到了换行符（比如按下回车键），那么fgets()读取到的数据就是： 已经读到的数据（遇到换行符前的数据）+换行符+NULL



strlen()函数返回的size_t类型，他是一个无符号整数类型。
这就带来了一些需要注意的问题。
比如
1  if( strlen(str1) >= strlen(str2) )......
2  if( strlen(str1) - strlen(str2) >= 0)......

你可能会觉得这两句没有区别，但事实上，他们的完全不一样！

第一个  if 能够正常工作，但是第二个就不能了，因为strlen（）的返回值是 无符号整型值所以 >=  左边的整个式子的结果就是 无符号整型的。也就是说 第二个if 的

结果永远都为真，（无符号数不可能大于0）。



