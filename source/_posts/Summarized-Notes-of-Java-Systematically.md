---
title: Summarized Notes of Java Systematically
description: Java 系统的总结笔记
top: true
date: 2020-06-01 10:44:54
categories: Java
tags: Java
---

<img src="https://4a7efb2d53317100f611-1d7064c4f7b6de25658a4199efb34975.ssl.cf1.rackcdn.com/patch-or-perish-java-ftc-tells-oracle-showcase_image-1-p-2014.jpg" width="100%"/>

<!-- more -->

# Java 语言基础

## 数据类型

### 基本数据类型

Java 中的数值类型都是有符号的，取值范围是固定的，不会随着机器硬件环境或者操作系统的改变而改变。

#### 为什么需要基本数据类型

我们都知道在Java语言中，`new`一个对象是存储在堆里的，我们通过栈中的引用来使用这些对象；所以，对象是比较消耗资源的。

对于经常用到的类型，如 int 等，如果我们每次使用这种变量的时候都需要 new 一个对象的话，就会比较笨重。所以，和 C++ 一样，Java提供了基本数据类型，这种数据的变量不需要使用 new 创建，他们不会在堆上创建，而是直接存储在栈内存中，因此会更加高效。

#### 包装类型

Java 是一个面向对象的语言，但基本数据类型却不是面向对象的，这在实际使用时存在很多的不便，为了解决这个不足，在设计类时为每个基本数据类型设计了一个对应的类，这样八个和基本数据类型对应的类统称为包装类(Wrapper Class)。

##### 为什么需要包装类

Java 是一种面向对象语言，很多地方都需要使用对象而不是基本数据类型。比如，在集合类中，我们是无法将 int 、double 等类型放进去的。因为集合要求其元素是 Object 类型。

为了让基本类型也具有对象的特征，就出现了包装类型，它相当于将基本类型“包装起来”，使得它具有了对象的性质，并且为其添加了属性和方法，丰富了基本类型的操作。

##### 自动装箱/拆箱

###### 自动装箱与自动拆箱的实现原理

```java
public static  void main(String[]args){
    Integer integer=1; //装箱
    int i=integer; //拆箱
}
```

对以上代码进行反编译后可以得到以下代码：

```java
public static  void main(String[]args){
    Integer integer=Integer.valueOf(1); 
    int i=integer.intValue(); 
}
```

从上面反编译后的代码可以看出，int 的自动装箱都是通过`Integer.valueOf()`方法来实现的，Integer 的自动拆箱都是通过`integer.intValue()`来实现的。其他类型也有对应的方法。

> 自动装箱都是通过包装类的`valueOf()`方法来实现的，自动拆箱都是通过包装类对象的`xxxValue()`来实现的。

###### 容易被忽略的自动拆装箱场景

- Java 中的集合类只能接收对象类型，当我们把基本数据类型放入集合类中的时候，会进行自动装箱。

- 包装类与基本数据类型进行比较运算，是先将包装类拆箱成基本数据类型，然后进行比较的。

- 两个包装类型之间的运算，会自动拆箱成基本类型进行。

- 三目运算符的语法规范中定义，当第二、三位操作数分别为基本类型和对象时，其中的对象就会拆箱为基本类型进行操作。

  > [自动拆箱导致空指针异常](http://www.hollischuang.com/archives/435)
  >
  > ```java
  > Map<String,Boolean> map =  new HashMap<String, Boolean>();
  > Boolean b = (map!=null ? map.get("test") : false);
  > ```
  >
  > 上述代码运行后报 NPE：NullPointerException，查看反编译结果如下
  >
  > ```java
  > HashMap map = new HashMap();
  > Boolean b = Boolean.valueOf(map == null ? false : ((Boolean)map.get("test")).booleanValue());
  > ```
  >
  > hashmap.get(“test”)->null;
  >
  > (Boolean)null->null;
  >
  > null.booleanValue()->报错

- 函数参数与返回值

###### 自动装箱与缓存

在 Boxing Conversion 部分的 Java 语言规范(JLS)规定如下：

如果一个变量 p 的在以下范围内时：

- -128至127之间的整数(§3.10.1) 

- true 和 false的布尔值 (§3.10.3)

- ‘\u0000’至 ‘\u007f’之间的字符(§3.10.4)

将 p 包装成对象时，会直接使用缓存中的对象。

###### 自动拆装箱带来的问题

- 包装对象的数值比较，不能简单的使用`==`，虽然 -128 到 127 之间的数字可以，但是这个范围之外的还是需要使用`equals`比较。

- 由于自动拆箱，如果包装类对象为 null，那么自动拆箱后就有可能抛出NPE。

- 循环中有大量拆装箱操作，会浪费很多资源。

## 序列化与反序列化

序列化（Serialization）是将对象的状态信息转换为可以存储或传输的形式的过程。

反序列化（Unserialization）则是序列化的反过程。

在 Java 中，对象的序列化与反序列化被广泛应用到 RMI(远程方法调用)及网络传输中。

### 相关接口及类

> java.io.Serializable
> java.io.Externalizable
> java.io.ObjectOutputStream
> java.io.ObjectInputStream
> java.io.ObjectOutput
> java.io.ObjectInput

#### Serializable 接口

类通过实现 `java.io.Serializable` 接口来启用序列化功能。未实现此接口的类将无法使其任何状态序列化或反序列化。**可序列化类的所有子类型本身都是可序列化的**。此接口没有方法或字段，仅**用于标识**可序列化的语义。

如果没有实现 Serializable 接口，序列化时将抛出 `NotSerializableException`。

如果要将在父类中的变量持久化下来，那么父类也应该实现`java.io.Serializable`。

**序列化**

```java
ObjectInputStream ois = null;
try {
  ois = new ObjectInputStream(new FileInputStream(fileName));
  Test1 o = (Test1) ois.readObject();
  System.out.println(o);
} catch (IOException | ClassNotFoundException e) {
  e.printStackTrace();
}finally {
  try {
    assert ois != null;
    ois.close();
  } catch (IOException e) {
    e.printStackTrace();
  }
}
```

**反序列化**

```java
ObjectOutputStream oos = null;
try {
  oos = new ObjectOutputStream(new FileOutputStream(fileName));
  oos.writeObject(test1);
} catch (IOException e) {
  e.printStackTrace();
} finally {
  try {
    assert oos != null;
    oos.close();
  } catch (IOException e) {
    e.printStackTrace();
  }
}
```

#### Externalizable 接口

通过实现 Externalizable 接口的方式实现序列化时

- 如果类中没有空构造器，反序列化时则会报错 `java.io.InvalidClassException: xxx.xxx.xxx; no valid constructor`
- 如果没有实现接口的 `writeExternal()` 或 `readExternal()` 方法，则会将 fields 序列化/反序列化为默认值(int:0 Object:null)

#### Transient 关键字

在变量声明前加上该关键字，可以阻止该变量被序列化到文件中，在被反序列化后，transient 变量的值将被设为默认值，如 int 型的是 0，对象型的是 null。

#### 序列化ID

虚拟机是否允许反序列化，不仅取决于类路径和功能代码是否一致，还要确认两个类的序列化 ID 是否一致（`private static final long serialVersionUID`)

序列化 ID 有两种生成策略，一个是固定的 1L，一个是随机生成一个不重复的 long 类型数据（实际上是使用 JDK 工具生成）。

如果没有特殊需求，用默认的 1L 就可以，这样可以确保代码一致时反序列化成功。随机生成的序列化 ID 可以用来限制某些用户的使用。

### 序列化与单例模式

> [单例与序列化的那些事儿](https://cloud.tencent.com/developer/article/1341386)

#### 为什么序列化可以破坏单例模式？

序列化会通过反射调用无参构造器创建新对象，进而破坏单例模式。

`ObjectInputStream.readObject()` 的调用栈：

ObjectInputStream.readObject() ---> ObjectInputStream.readObject0() ---> ObjectInputStream.readOrdinaryObject() ---> ObjectInputStream.checkResolve()

```java
private Object readOrdinaryObject(boolean unshared) throws IOException {
  
  ...

  Object obj;
  try {
    // desc.isInstantiable() 是否实现了 Serializable/Externalizable 接口
    // desc.newInstance() 通过反射调用无参构造方法
    obj = desc.isInstantiable() ? desc.newInstance() : null;
  } catch (Exception ex) {
    throw (IOException) new InvalidClassException(
      desc.forClass().getName(),
      "unable to create instance").initCause(ex);
  }

  ...

    // desc.hasReadResolveMethod() 是否包含 readResolve() 方法
    if (obj != null &&
        handles.lookupException(passHandle) == null &&
        desc.hasReadResolveMethod())
    {
      // desc.invokeReadResolve(obj) 通过反射调用 readResolve() 方法。
      Object rep = desc.invokeReadResolve(obj);
      if (unshared && rep.getClass().isArray()) {
        rep = cloneArray(rep);
      }
      if (rep != obj) {
        // Filter the replacement object
        if (rep != null) {
          if (rep.getClass().isArray()) {
            filterCheck(rep.getClass(), Array.getLength(rep));
          } else {
            filterCheck(rep.getClass(), -1);
          }
        }
        handles.setObject(passHandle, obj = rep);
      }
    }

  return obj;
}
```

#### 如何防止序列化破坏单例模式

只要在类中定义 `readResolve()` 方法返回单例即可。

```java
package cn.gzhennaxia.test.io.serializable;

import java.io.Serializable;

public class Singleton implements Serializable {

  private volatile static Singleton singleton;

  private Singleton() {
  }

  /**
     * 使用双重校验锁实现单例模式
     *
     * @author bli@skystartrade.com
     * @date 2020-06-04 15:24
     */
  public static Singleton getInstance() {
    if (singleton == null) {
      synchronized (Singleton.class) {
        if (singleton == null) {
          singleton = new Singleton();
        }
      }
    }
    return singleton;
  }

  /**
     * 反序列化时会检查是否存在 readResolve() 方法，存在则调用它来实例化
     * 否则使用反射调用无参构造器实例化，因此可以通过创建 readResolve() 方法
     * 来保护单例在反序列化过程中不被破坏。
     *
     * @author bli@skystartrade.com
     * @date 2020-06-04 16:33
     */
  public Object readResolve() {
    return singleton;
  }
}
```

