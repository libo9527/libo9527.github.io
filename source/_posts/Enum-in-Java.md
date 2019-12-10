---
title: Enum in Java
description: Java 中的枚举类型
date: 2019-12-05 11:04:47
categories: Java
tags: Enum
---

<img src="https://www.edureka.co/blog/wp-content/uploads/2019/06/Enumeration-in-Java.jpg" width="100%"/>

<!-- more -->

## 枚举

> [枚举类- 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1260473188087424)

### 枚举常量与普通常量的比较

Java 中通过`static final`来定义普通常量，与普通常量相比，用枚举类定义的常量拥有以下特点。

- `enum` 常量本身带有类型信息，即`Weekday.SUN`类型是`Weekday`，编译器会自动检查出类型错误。
  - 因为枚举常量拥有类型信息，所以不同类型的枚举常量不能相互比较合赋值

### 枚举类型与其他引用类型的区别

定义一个枚举类 Color：

```java
public enum Color {
    RED, GREEN, BLUE;
}
```

反编译后的结果：

```java
public final class Color extends Enum { // 继承自Enum；标记为final class，不能被继承
  	// 每个实例均为全局唯一
    public static final Color RED = new Color();
    public static final Color GREEN = new Color();
    public static final Color BLUE = new Color();
	  // 私有构造方法，确保外部无法使用 new 操作符来创建实例
    private Color() {}
}
```

由反编译结果可以看出枚举类型有如下特点：

- 枚举类型也是引用类型；
- 定义的`enum`类型总是继承自`java.lang.Enum`，且无法被继承；
- 只能定义出`enum`的实例，而无法通过`new`操作符创建`enum`的实例；
- 定义的每个实例都是引用类型的唯一实例。

其次 Java 中的枚举类型还有以下特点：

- 可以将`enum`类型用于`switch`语句；
- 与普通的引用类型相比，枚举常量不必使用 equals 方法进行比较，直接使用 `==` 比较即可。这是因为 `enum` 类型的每个常量在JVM中只有一个唯一实例。

### 使用枚举时的注意事项

- 不要使用 `toString()`，通过 `name()` 来获取常量定义的字符串；
- enum 的构造方法必须声明为 private，否则编译报错；
- 枚举类的字段可以是非final类型，即可以在运行期修改，但是不推荐这样做！