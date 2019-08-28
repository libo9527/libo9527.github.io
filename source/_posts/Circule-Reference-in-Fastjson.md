---
title: Circule Reference in Fastjson
description: 关于 Fastjson 中的循环引用问题
date: 2019-08-17 12:08:04
categories: Fastjson
tags: Fastjson
---

<img src="https://i.loli.net/2019/08/17/YvAZiPnxFgtGj6X.png" width="100%"/>

<!-- more -->

> [SerializerFeature_DisableCircularReferenceDetect_cn | fastjson-GitHub](https://github.com/alibaba/fastjson/wiki/SerializerFeature_DisableCircularReferenceDetect_cn)
>
> 在 fastjson 中，会自动检测循环引用，并且输出为 fastjson 专有的引用表示格式。但这个不能被其他 JSON 库识别，也不能被浏览器识别，所以 fastjson 提供了关闭循环引用检测的功能。

## 问题描述

项目中的一个接口返回的 json 数据出现引用格式：

```json
"lastDoneCollectTask":{
  "$ref":"$.data.lastCreateCollectTask"
}
```

## 问题原因

项目中将 Spring Boot 内置的 JSON 改成了 fastjson，而 fastjson 会自动进行重复引用/循环引用检测，并将循环应用改为自己定义的引用格式。

## 解决方案

关闭 fastjson 循环引用检测的功能

```java
JSON.toJSONString(obj, SerializerFeature.DisableCircularReferenceDetect);
```

##  ~~疑问🤔️~~

> 引用分为两种，重复引用和循环引用
>
> 重复引用指一个对象重复出现多次，循环引用指相互引用，，这个问题比较严重，如果处理不好就会出现StackOverflowError异常

举个例子，有两个类分别如下：

```java
public class A {

    private int id;

    private B b1;

    private B b2;
}
```

```java
public class B {

    private int id;
}
```

执行如下代码返回的结果中依然存在引用，难道这也属于循环引用吗

```java
public static void main(String[] args) {
  A a = new A();
  B b = new B();
  b.setId(123);
  a.setB1(b);
  a.setB2(a.getB1());

  System.out.println(JSON.toJSONString(a));
  // {"b1":{"id":123},"b2":{"$ref":"$.b1"},"id":0}
}
```

