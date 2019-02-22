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



sort() 函数的使用



### [*C++* error 2872:*不明确的符号* - code_nie的博客 - CSDN博客](https://www.baidu.com/link?url=HvAoxxP6Vh5jlJN6_5JVIgqn4V5H04hTcU8KEF6ctZDJEEgE7k-y7GsouW6h_IlqUgHTgYR1YWeomMvxU3_yM0_i8Lmz4EJGseKSohh6zR_&wd=&eqid=eec0d1d100080620000000065c6bbaed)

简单地说，出现这种情况是因为程序使用的命名空间里已经有这个变量名了

namespace std,  "rank"



vs2010 报错：

1>MSVCRTD.lib(crtexe.obj) : error LNK2019: 无法解析的外部符号 _main，该符号在函数 ___tmainCRTStartup 中被引用
1>f:\visual studio 2010\Projects\pat\Debug\pat.exe : fatal error LNK1120: 1 个无法解析的外部命令



原因：main 拼错了，拼成了 mian



strcpy() 函数

## 考点

深圳大学： 白鉴聪, szubjc@szu.edu.cn
Dev C++ 5.10; Code::Blocks16.01; Java SE Development Kit9.0.1; Eclipse Oxygen.2 版本 4.7.2; Python解释器（3.6.5）; PyCharm Community Edition



求余（%）和取整（/）优先级相同。



> [PAT备考经验&相关信息](https://blog.csdn.net/qq_38658814/article/details/82534765?utm_source=blogxgwz2)