---
title: Effective Java
description: Effective Java 笔记
comments: false
hidden: false
top: false
date: 2020-09-18 10:35:29
categories:
tags:
---

<img src="https://e5g4c4d6.rocketcdn.me/wp-content/uploads/2018/01/review-effective-java.jpg" width="100%"/>

<!-- more -->

> https://github.com/jbloch/effective-java-3e-source-code

## 服务提供者框架

> [源码|什么是服务提供者框架？举例？ | 程序猿说你好 - 猴子007](https://monkeysayhi.github.io/2017/09/21/源码|什么是服务提供者框架？举例？/)

服务提供者框架是只这样一个系统：多个服务提供者实现一个服务，系统为服务提供者的客户端提供多个实现，并把他们从多个实现中解耦出来。

服务提供者框架中有四个重要的组件：

- 服务接口（Service Interface）：由服务提供者实现，用来抽象服务提供者提供的服务。
- 提供者注册API（Provider Registration API）：系统用来注册实现，让客户端访问它们的。
- 服务访问API（Service Access API）：客户端用来获取服务的实例。
- 服务提供者接口（Service Provider Interface）：可选，由服务提供者实现，用来抽象服务提供者。

### JDBC 中的应用

- 服务接口：Connection
- 服务提供者接口：Driver
- 提供者注册API：DriverManager.registerDriver() 方法
- 服务访问API：DriverManager.getConnection() 方法

### 服务接口

Connection 即为数据库连接服务。

```java
public interface Connection  extends Wrapper, AutoCloseable {

}
```

### 服务提供者接口

Driver 提供 Connection 服务实例：

```java
public interface Driver {

  Connection connect(String url, java.util.Properties info) throws SQLException;

}
```

### 提供者注册API

服务的提供者实现 Driver 接口，通过 DriverManager.registerDriver() 将服务提供者的实例注册到 DriverManager 中：

```java
public class DriverManager {
  
  public static synchronized void registerDriver(java.sql.Driver driver)
    throws SQLException {
    registerDriver(driver, null);
  }
  
  public static synchronized void registerDriver(java.sql.Driver driver, DriverAction da)
    throws SQLException {
    /* Register the driver if it has not already been added to our list */
    if(driver != null) {
      registeredDrivers.addIfAbsent(new DriverInfo(driver, da));
    } else {
      // This is for compatibility with the original DriverManager
      throw new NullPointerException();
    }
    println("registerDriver: " + driver);
  }

}
```

具体的注册行为由服务提供者发起。以 MySQL 的 Driver 实现 com.mysql.jdbc.Driver 为例：

```java
public class Driver extends NonRegisteringDriver implements java.sql.Driver {

  static {
    try {
      java.sql.DriverManager.registerDriver(new Driver());
    } catch (SQLException E) {
      throw new RuntimeException("Can't register driver!");
    }
  }

  public Driver() throws SQLException {
    // Required for Class.forName().newInstance()
  }

}
```

可知，在第一次加载 com.mysql.jdbc.Driver 时，com.mysql.jdbc.Driver会利用静态代码块调用提供者注册API `DriverManager.registerDriver()`。

### 服务访问API

注册服务提供者的实例后，服务提供者就能够通过框架提供服务。具体来说，可通过静态工厂方法 `DriverManager.getConnection()` 提供Connection 服务，底层是通过 Driver 接口的实现类实现的：

```java
public class DriverManager {

  public static Connection getConnection(String url)
    throws SQLException {
    ...
      java.util.Properties info = new java.util.Properties();
    return (getConnection(url, info, Reflection.getCallerClass()));
  }

  //  Worker method called by the public getConnection() methods.
  private static Connection getConnection(
    ...
    for(DriverInfo aDriver : registeredDrivers) {
      // If the caller does not have permission to load the driver then
      // skip it.
      if(isDriverAllowed(aDriver.driver, callerCL)) {
        try {
          println("    trying " + aDriver.driver.getClass().getName());
          Connection con = aDriver.driver.connect(url, info);
          if (con != null) {
            // Success!
            println("getConnection returning " + aDriver.driver.getClass().getName());
            return (con);
          }
        } catch (SQLException ex) {
          if (reason == null) {
            reason = ex;
          }
        }
        ...
      } else {
        println("    skipping: " + aDriver.getClass().getName());
      }
      ...
    }
  }
}
```

这里的逻辑很简单，遍历所有已注册的Driver实现类，**选择第一个可用的Driver**创建Connection。还记得 `Driver#connect()` 方法吗？`aDriver.driver.connect` 一句中，**由底层Driver完成Connection的创建**。

## WeakHashMap

> [Java中的WeakHashMap - 后端- 掘金](https://juejin.im/entry/6844903510652239879)
>
> [Java 集合系列13之WeakHashMap详细介绍(源码解析)和使用 ...](https://wangkuiwu.github.io/2012/02/13/collection-13-weakhashmap/)

### 自动回收特性

在 WeakHashMap 中，key 的类型是 WeakReference。如果对应的 key 被回收，则这个 key 指向的 value 会被从 Map 容器中移除。

```java
public static void main(String[] args) throws InterruptedException {
  String key = new String("k1");
  String key2 = "k2";
  String value = "v1";
  String value2 = "v2";
  WeakHashMap<String, String> map = new WeakHashMap<>();
  map.put(key,value);
  map.put(key2,value2);

  System.out.println(map.size());
  System.out.println(map);

  key = null;

  System.gc();
  Thread.sleep(1000);

  System.out.println(map.size());
  System.out.println(map);

  key2 = null;

  System.gc();
  Thread.sleep(1000);

  System.out.println(map.size());
  System.out.println(map);
}
```

结果

```
2
{k2=v2, k1=v1}
1
{k2=v2}
1
{k2=v2}
```

#### Q：当key是以字面量方式赋值时，并不会自动回收

字面量赋值时是引用的常量池里的对象，GC 不会回收，即不会讲该 key 存入 queue，expungeStaleEntries 自然也不会清除掉对应的 entity。

### 利用 WeakHashMap 实现缓存

WeakHashMap 的特性比较适合实现类似本地、堆内缓存的存储机制——缓存的失效依赖于 GC 收集器的行为。假设一种应用场景：我们需要保存一批大的图片对象，其中 values 是图片的内容，key 是图片的名字，这里我们需要选择一种合适的容器保存这些对象。

使用普通的 HashMap 并不是好的选择，这些大对象将会占用很多内存，并且还不会被GC回收，除非我们在对应的 key 废弃之前主动 remove 掉这些元素。WeakHashMap 非常适合使用在这种场景下，下面的代码演示了具体的实现：

```java
WeakHashMap<UniqueImageName, BigImage> map = new WeakHashMap<>();
BigImage bigImage = new BigImage("image_id");
UniqueImageName imageName = new UniqueImageName("name_of_big_image"); //强引用

map.put(imageName, bigImage);
assertTrue(map.containsKey(imageName));

imageName = null; //map中的values对象成为弱引用对象
System.gc(); //主动触发一次GC

await().atMost(10, TimeUnit.SECONDS).until(map::isEmpty);
```

首先，创建一个WeakHashMap对象来存储 BigImage 实例，对应的 key 是UniqueImageName 对象，保存到 WeakHashMap 里的时候，key 是一个弱引用类型。

然后，我们将 imageName 设置为null，这样就没有其他强引用指向bigImage 对象，按照 WeakHashMap 的规则，在下一次GC周期中会回收bigImage 对象。

通过 System.gc() 主动触发一次GC过程，然后可以发现 WeakHashMap 成为空的了。

## No 22

### 二进制兼容性

> [What is binary compatibility in Java? - Stack Overflow](https://stackoverflow.com/questions/14973380/what-is-binary-compatibility-in-java)

22条提到如果在接口中定义了常量，然后在其他类中需要使用时，实现这个接口，然后就可以用了，但是如果之后不用这些常量了，为了二进制兼容性，还需要实现这个接口。

这里提到的二进制兼容性，简单理解就是不需要重新编译没有改动的类。

即你在接口A中定义了常量，然后类B实现了这个接口，类C又继承了类B，在类B，C中都有用到这个常量，之后有一天你的B类不再需要使用这个常量了，但如果你删除了B类中的实现声明，那就会导致C类编译失败！

```java
public interface A {

  int CONSTANT = 1;
}
```

```java
public class B implements A {

  public void funB() {
    System.out.println(CONSTANT);
  }
}
```

```java
public class C extends B {

  public void funC() {
    System.out.println(CONSTANT);
  }
}
```

B not need A.CONSTANT any more, delete implements.

```java
//public class B implements A {
public class B {

    public void funB() {
//        System.out.println(CONSTANT);
    }
}
```

Then C will cpmpile failed.

## No 24

### 嵌套类

> [Effective Java-嵌套类(内部类) - 云+社区- 腾讯云](https://cloud.tencent.com/developer/article/1482981)

## No 28

### 为什么创建泛型数组是非法的？

因为泛型数组不是**类型安全**的。如果它合法，那么编译器自动添加的类型转换在运行期间可能会抛出 ClassCastException 异常。

```java
// 假设能创建泛型数组
/* (1) */ List<String>[] stringLists = new List<String>[1];
/* (2) */ List<Integer> intList = List.of(42);
/* (3) */ Object[] objects = stringLists;
/* (4) */ objects[0] = intList;
/* (5) */ String s = stringLists[0].get(0);
```

假设第 1 行是合法的，创建了一个泛型数组。

第 2 行穿件并初始化了一个包含单个元素的 List\<Integer\>。

第 3 行将 List\<String\> 数组保存到一个 Object 数组变量中，这是合法的，因为数组是**协变**的，即 List\<String\> 是 Object 的子类，所以 List\<String\>[] 也是 Object[] 的子类。

第 4 行将 List\<Integer\> 保存到 Object 数组中，这是合法的，因为泛型是通过擦除实现的，List\<Integer\> 在运行时类型只是 List。

第 5 行在运行时将抛出 ClassCastException 异常。因为编译器会添加上类型转化 `String s = (String) stringLists[0].get(0);`

我们将第 5 行拆分开来看：

```java
// String s = stringLists[0].get(0);
List list = stringLists[0];
Object object = list.get(0);
String s = (String) object;
```

由于第 4 行将 List<Integer\> 实例保存到了原本只包含 List\<String\> 实例的数组中，所以这里的 object 实际上是 Integer 类型，所以在转化为 String 类型时将抛出异常。

### 为什么 Java 将泛型设置为不可变的？

我们知道 Java 中的数组是可变的，泛型是不可变的，即如果 Sub 是 Super 的子类，那么 Sub[] 就是 Super[] 的子类，但是 List\<Sub\> 却不是 List\<Super\> 的子类！

那么为什么 Java 要将泛型设置为不可变的呢？原因是为了兼容，Java 使用了类型擦除的方式实现泛型，由于存在类型擦除，所以为了类型安全，将泛型设计为不可变。

```java
/* (1) */ List<String> stringList = new ArrayList<String>();
/* (2) */ List<Object> objectList = stringList;
/* (3) */ objectList.add(12);
/* (4) */ String s = stringList.get(0);
```

假设泛型是可变的，那么第 2 行代码就是合法的，那么第 4 行代码就会在运行时抛出 ClassCastException 异常。

