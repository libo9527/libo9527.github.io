---
title: Study Notes of JAVA
description: JAVA学习笔记
hidden: true
date: 2019-01-07 09:49:23
categories:
 - Study Notes
 - JAVA
---

<img src="https://www.imarslan.com/wp-content/uploads/2018/06/cover.png" width="100%"/>

<!-- more -->

## 包装类

### Integer 与 int

Integer 是类，提供了很多方法方便使用，而 int 可以保证没有空指针异常，初始值为 0。

## 泛型

> [Java 泛型| 菜鸟教程](http://www.runoob.com/java/java-generics.html)
>
> [Java泛型详解：和Class的使用。泛型类，泛型方法的详细使用实例](https://blog.csdn.net/qq_27093465/article/details/73229016)

### 简介

​	Java 泛型（generics）是 JDK 5 中引入的一个新特性, 泛型提供了编译时类型安全检测机制，该机制允许程序员在编译时检测到非法的类型。

​	**泛型的本质是参数化类型**，即将类型由原来具体的类型参数化。这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。**泛型类型在逻辑上可以看成是多个不同的类型，实际上都是相同的基本类型。**

​	ArrayList 就是泛型的典型应用。

​	Java泛型很大程度上只能提供静态类型检查，然后类型的信息就会被擦除。类型擦除就是指 Java 泛型只能用于在编译期间的静态类型检查，然后编译器生成的代码会擦除相应的类型信息（即类型均变为 Object），这样到了运行期间实际上JVM根本就知道泛型所代表的具体类型。这样做的目的是因为Java泛型是1.5之后才被引入的，为了保持向下的兼容性，所以只能做类型擦除来兼容以前的非泛型代码。

### 相比 Object，泛型的优势

1. 不用强制转换
2. 避开运行时的类型转换错误。在 settValue 时如果传入类型不对，编译时便会报错。

### 字母规范

虽然在定义泛型时，可以使用任意字母，其意义完全相同，单为了提高可读性，还是用有意义的字母比较好，一般来讲，在不同的情境下使用的字母意义如下：

-  E — Element，常用在 java Collection 里，如：List<E>,Iterator<E>,Set<E>
-  K,V — Key，Value，代表Map的键值对
-  N — Number，数字
-  T — Type，类型，如 String，Integer 等等

### ? 和 T 的区别

​	`? extends E`: 接收E类型或者E的子类型。`? super E`: 接收E类型或者E的父类型

​	？和T都表示不确定的类型 ，但如果是 T 的话，函数里面可以对 T 进行操作，比方 `T car = getCar()`，而不能用 `？ car = getCar()`。

### 注意事项

1. Java 中不允许创建泛型数组

## 集合

### Collections

#### nCopies()

```java
public static void main(String[] args) {
  List<Integer> list = new ArrayList<>(
    Collections.nCopies(3, 0));
  System.out.println(list);
  // [0, 0, 0]
}
```

#### fill()

```java
public static void main(String[] args) {
  List<Integer> list = new ArrayList<>(
    Collections.nCopies(3, 0));
  System.out.println(list);
  // [0, 0, 0]
  Collections.fill(list, 1);
  System.out.println(list);
  // [1, 1, 1]
}
```

Collections.nCopies() 只能在创建 List 时填充容器，Collections.fill() 是用来对一个已有容器的填充。

## 线程

### 线程池
