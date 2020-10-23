---
title: Enumerations in Java
description: Java 枚举
comments: false
hidden: false
top: false
date: 2020-10-23 15:06:51
categories:
tags:
---

<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcRrRiUnBB0OyvfKqKFrGihpnhVmsNqE2ALoqg&usqp=CAU" width="100%"/>

<!-- more -->

```java
public enum Color {

  RED, GREEN, BLUE;

  static final String STR = "STR";
  static String str = "str";
  static {
    System.out.println(RED);
  }

  Color() {
    System.out.println(STR);
    // 编译报错：Cannot refer to the static enum field Color.str within an initializer
    // System.out.println(str);
  }
}
```

```java
static {
  RED = new Color("RED", 0);
  GREEN = new Color("GREEN", 0);
  BLUE = new Color("BLUE", 0);
  $VALUES = new Color[3];
  $VALUES[0] = RED;
  $VALUES[1] = GREEN;
  $VALUES[2] = BLUE;
  str = "str";
  System.out.println(RED);
}
```

通过反编译可以看到枚举对象的构造和赋值先于所有的 `static` 代码（静态变量/静态代码块）。

普通的类是先完成类的初始化，然后才构造对象，构造对象的时候所有的静态成员都已经准备好了；

但是枚举不同，枚举类初始化的第一步就是要构造所有的枚举对象，也就是说，枚举对象构造时，静态成员还完全没有准备好，这也是为什么**在枚举对象的构造函数中，引用非 final 的 static 成员会编译不通过**。

