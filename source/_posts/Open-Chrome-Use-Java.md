---
title: Open Chrome Use Java
description: Java 打开Chrome的几种方式
comments: false
hidden: false
top: false
date: 2022-02-08 09:37:05
categories:
tags:
---

<img src="cover" width="100%"/>

<!-- more -->

# Java 打开 Chrome 的几种方式

> [Java实现打开浏览器的N种办法_爱的叹息的专栏 - CSDN博客](https://blog.csdn.net/zp357252539/article/details/77896257)

## Java 创建进程的方式打开指定网址

```java
/**
 * Java打开浏览器的几种方式
 * error=13, Permission denied
 */
private static void test02() {
    ProcessBuilder pb = new ProcessBuilder("/Applications/Google Chrome.app", "https://www.baidu.com");
    try {
        pb.start();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

## 调用默认浏览器打开指定网址

```java
/**
 * Java打开浏览器的几种方式
 * 这种方式可以，会调用默认浏览器打开网页
 * 但是在Spring Boot项目中Desktop.getDesktop()会报空指针
 */
private static void main() {
    URI uri = URI.create("https://www.baidu.com");
    Desktop desktop = Desktop.getDesktop();
    if (desktop.isSupported(Desktop.Action.BROWSE)){
        try {
            desktop.browse(uri);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 执行终端命令

```java
/**
 * 执行终端命令
 */
private static void main() {
    try {
        Runtime.getRuntime().exec("open https://www.baidu.com");
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```



