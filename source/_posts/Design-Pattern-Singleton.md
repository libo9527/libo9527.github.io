---
title: Design Pattern - Singleton
description: 设计模式之单例模式
comments: false
hidden: false
top: false
date: 2020-10-22 16:23:41
categories:
tags:
---

<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcSEYmhD44QXA-K_w4fBjzKvdjkiZ2dcl55lbA&usqp=CAU" width="100%"/>

<!-- more -->

## 实现方式

### 饿汉模式

```java
public class Singleton1 {

  private static Singleton1 instance = new Singleton1();

  private Singleton1() {
  }

  public static Singleton1 getInstance() {
    return instance;
  }
}
```

### 懒汉模式

```java
public class Singleton2 {

  private static Singleton2 instance;

  private Singleton2() {
  }

  public static Singleton2 getInstance() {
    if (instance == null) {
      instance = new Singleton2();
    }
    return instance;
  }
}
```

### 线程安全的懒汉模式

```java
public class Singleton3 {

  private static Singleton3 instance;

  private Singleton3() {
  }

  public static synchronized Singleton3 getInstance() {
    if (instance == null) {
      instance = new Singleton3();
    }
    return instance;
  }
}
```

### 双重校验锁模式（DCL，即 double-checked locking）

```java
public class Singleton4 {

  private volatile static Singleton4 instance;

  private Singleton4() {
  }

  public static Singleton4 getInstance() {
    if (instance == null) {
      synchronized (Singleton4.class) {
        if (instance == null) {
          instance = new Singleton4();
        }
      }
    }
    return instance;
  }
}
```

为什么要加 volatile 关键字？

为什么要两次判空？

### 静态内部类单例模式

```java
public class Singleton5 {

  private Singleton5() {
  }

  public static Singleton5 getInstance() {
    return InnerClass.instance;
  }

  private static class InnerClass {
    private static Singleton5 instance = new Singleton5();
  }
}
```

静态内部类如何保证线程安全的？

### 枚举单例模式

```java
public enum Singleton6 {
  INSTANCE;
}
```

## 反射攻击

私有化构造器并不能阻止反射攻击。

```java
public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
  Singleton1 instance = Singleton1.getInstance();

  Constructor<Singleton1> declaredConstructor = Singleton1.class.getDeclaredConstructor();
  declaredConstructor.setAccessible(true);
  Singleton1 instance1 = declaredConstructor.newInstance();
  System.out.println(instance == instance1);
}
```

```
false

Process finished with exit code 0
```



## 序列化



## 总结

|                          | 饿汉模式                            | 懒汉模式                            | 线程安全的懒汉模式                  | 双重校验锁模式                      | 静态内部类单例模式                  | 枚举单例模式 |
| ------------------------ | ----------------------------------- | ----------------------------------- | ----------------------------------- | ----------------------------------- | ----------------------------------- | ------------ |
| 延迟加载                 | <span style="color: red"/>否</span> | 是                                  | 是                                  | 是                                  | 是                                  | 是           |
| 线程安全                 | 是                                  | <span style="color: red"/>否</span> | 是                                  | 是                                  | 是                                  | 是           |
| 反射攻击<br />(能否抵御) | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | 能           |
| 序列化                   |                                     |                                     |                                     |                                     |                                     |              |

