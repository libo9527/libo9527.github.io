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

<img src="cover" width="100%"/>

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

