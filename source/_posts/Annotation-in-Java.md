---
title: Annotation in Java
description: Java 的注解
comments: false
hidden: false
top: false
date: 2020-10-22 10:57:36
categories:
tags:
---

<img src="https://ditchthattextbook.com/wp-content/uploads/2018/03/redefining-annotation-ditch-that-pdf-and-hyper-annotate-1-1.png" width="100%"/>

<!-- more -->

> [JAVA 注解的基本原理 - 掘金](https://juejin.im/post/6844903636733001741)

### 注解的本质

反编译后可以看到注解就是继承自 `java.lang.annotation.Annotation` 的一个接口。

注解只不过是一种特殊的注释而已，如果没有解析它的代码，可能连注释都不如。

而解析一个类或者方法的注解往往有两种形式，一种是编译期直接的扫描，一种是运行期反射。

反射的事情我们待会说，而编译器的扫描指的是编译器在对 java 代码编译字节码的过程中会检测到某个类或者方法被一些注解修饰，这时它就会对于这些注解进行某些处理。

典型的就是注解 `@Override`，一旦编译器检测到某个方法被修饰了 `@Override` 注解，编译器就会检查当前方法的方法签名是否真正重写了父类的某个方法，也就是比较父类中是否具有一个同样的方法签名。这一种情况只适用于那些编译器已经熟知的注解类，比如 JDK 内置的几个注解，而自定义的注解，编译器是不知道你这个注解的作用的，当然也不知道该如何处理，往往只是会根据该注解的作用范围来选择是否编译进字节码文件，仅此而已。

