---
title: Design Pattern - Template Method
description: 设计模式之模版方法
comments: false
hidden: false
top: false
date: 2020-08-26 11:38:54
categories: Design Pattern
tags:
---

<img src="data:image/png;base64,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" width="100%"/>

<!-- more -->

## 模版方法模式

> 模版方法模式在一个方法中定义一个算法的骨架，而将一些步骤延迟到子类中。模版方法使得子类可以在不改变算法结构的情况下，重新定义算法中的某些步骤。——《Head First 设计模式（中文版）》

### 简单例子

冲泡咖啡和茶

咖啡冲泡步骤：

1. 把水煮沸
2. 用沸水冲泡咖啡
3. 把咖啡倒进杯子
4. 加糖和牛奶

茶冲泡步骤：

1. 把水煮沸
2. 用沸水浸泡茶叶
3. 把咖啡倒进杯子
4. 加柠檬

把共有的步骤及其顺序抽象为抽象类中的模版方法，可以共享的步骤放在抽象类中作为普通方法，需要子类实现的作为抽象方法。

#### 抽象类 CaffeineBerverage

```java
public abstract class CaffeineBeverage {

  public final void prepareRecipe() {
    boilWater();
    brew();
    pourInCup();
    addCondiments();
  }

  public abstract void brew();

  public abstract void addCondiments();

  public void boilWater() {
    System.out.println("Boiling water");
  }

  public void pourInCup() {
    System.out.println("Pouring into cup");
  }
}
```

#### 咖啡实现类

```java
public class Coffee extends CaffeineBeverage {

  @Override
  public void brew() {
    System.out.println("Steeping the coffee");
  }

  @Override
  public void addCondiments() {
    System.out.println("Adding Sugar and Milk");
  }
}
```

#### 茶实现类

```java
public class Tea extends CaffeineBeverage {

  @Override
  public void brew() {
    System.out.println("Steeping the tea");
  }

  @Override
  public void addCondiments() {
    System.out.println("Adding Lemon");
  }
}
```

#### 测试类

```java
public class Test {

  public static void main(String[] args) {
    CaffeineBeverage coffee = new Coffee();
    coffee.prepareRecipe();
    System.out.println("--------------------");
    CaffeineBeverage tea = new Tea();
    tea.prepareRecipe();
  }
}
```

```
Boiling water
Steeping the cffee
Pouring into cup
Adding Sugar and Milk
--------------------
Boiling water
Steeping the tea
Pouring into cup
Adding Lemon
```

### Java API 中的例子

`java.io.InputStream` 的 `read()` 方法，是抽象方法，需要由子类实现，而这个方法又会被 `read(byte b[], int off, int len)` 模版方法使用。

```java
package java.io;

public abstract class InputStream implements Closeable {

  public abstract int read() throws IOException;

  public int read(byte b[], int off, int len) throws IOException {
    if (b == null) {
      throw new NullPointerException();
    } else if (off < 0 || len < 0 || len > b.length - off) {
      throw new IndexOutOfBoundsException();
    } else if (len == 0) {
      return 0;
    }

    int c = read();
    if (c == -1) {
      return -1;
    }
    b[off] = (byte)c;

    int i = 1;
    try {
      for (; i < len ; i++) {
        c = read(); // 需要子类（例如 FileInputStream/ObjectInputStream）实现的方法！
        if (c == -1) {
          break;
        }
        b[off + i] = (byte)c;
      }
    } catch (IOException ee) {
    }
    return i;
  }
}
```

```java
package java.io;

public class FileInputStream extends InputStream {
  public int read() throws IOException {
    return read0();
  }

  private native int read0() throws IOException;
}
```

```java
package java.io;

public class ObjectInputStream extends InputStream implements ObjectInput, ObjectStreamConstants {

  private final BlockDataInputStream bin;

  public int read() throws IOException {
    return bin.read();
  }


  private class BlockDataInputStream extends InputStream implements DataInput    {
    public int read() throws IOException {
      if (blkmode) {
        if (pos == end) {
          refill();
        }
        return (end >= 0) ? (buf[pos++] & 0xFF) : -1;
      } else {
        return in.read();
      }
    } 
  }
}
```

### Java EE API 中的例子

> [Servlet入门- 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1304265949708322)

WEB 开发中常用 `HttpServlet` 里面的 `service` 方法处理请求，`service` 方法里面定义了调用流程，根据客户端调用的不同方式调用不同的方法，比如 `doGet`，`doPost`，`doDelete` 等实现 restful 调用，具体的 `doGet`，`doPost` 方法实现可以在自定义的 `Servlet` 中进行重写。 

但需要注意的是 `doGet`，`doPost`，`doDelete` 等并没有被定义为抽象方法。

```java
package javax.servlet.http;

public abstract class HttpServlet extends GenericServlet {
  protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String method = req.getMethod();
    long lastModified;
    if (method.equals("GET")) {
      lastModified = this.getLastModified(req);
      if (lastModified == -1L) {
        this.doGet(req, resp);
      } else {
        long ifModifiedSince = req.getDateHeader("If-Modified-Since");
        if (ifModifiedSince < lastModified) {
          this.maybeSetLastModified(resp, lastModified);
          this.doGet(req, resp);
        } else {
          resp.setStatus(304);
        }
      }
    } else if (method.equals("HEAD")) {
      lastModified = this.getLastModified(req);
      this.maybeSetLastModified(resp, lastModified);
      this.doHead(req, resp);
    } else if (method.equals("POST")) {
      this.doPost(req, resp);
    } else if (method.equals("PUT")) {
      this.doPut(req, resp);
    } else if (method.equals("DELETE")) {
      this.doDelete(req, resp);
    } else if (method.equals("OPTIONS")) {
      this.doOptions(req, resp);
    } else if (method.equals("TRACE")) {
      this.doTrace(req, resp);
    } else {
      String errMsg = lStrings.getString("http.method_not_implemented");
      Object[] errArgs = new Object[]{method};
      errMsg = MessageFormat.format(errMsg, errArgs);
      resp.sendError(501, errMsg);
    }
  } 
}
```

### Mybatis 中的例子

Mybatis 框架中的 BaseExecutor 类是一个基础的 SQL 执行类，实现了大部分 SQL 的执行逻辑，然后把几个方法交给子类定制化完成，例如模版方法 `queryFromDatabase` 中调用的 `doQuery` 方法就是一个抽象方法，需要子类提供实现。

```java
package org.apache.ibatis.executor;

public abstract class BaseExecutor implements Executor {

  protected abstract <E> List<E> doQuery(MappedStatement var1, Object var2, RowBounds var3, ResultHandler var4, BoundSql var5) throws SQLException;

  private <E> List<E> queryFromDatabase(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
    this.localCache.putObject(key, ExecutionPlaceholder.EXECUTION_PLACEHOLDER);

    List list;
    try {
      list = this.doQuery(ms, parameter, rowBounds, resultHandler, boundSql); // 抽象方法需要子类提供实现
    } finally {
      this.localCache.removeObject(key);
    }

    this.localCache.putObject(key, list);
    if (ms.getStatementType() == StatementType.CALLABLE) {
      this.localOutputParameterCache.putObject(key, parameter);
    }

    return list;
  }
}
```

![](http://www.uxys.com/u/cms/www/201912/01193214y9j2.jpg)

```java
package org.apache.ibatis.executor;

public class SimpleExecutor extends BaseExecutor {

  public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
    Statement stmt = null;

    List var9;
    try {
      Configuration configuration = ms.getConfiguration();
      StatementHandler handler = configuration.newStatementHandler(this.wrapper, ms, parameter, rowBounds, resultHandler, boundSql);
      stmt = this.prepareStatement(handler, ms.getStatementLog());
      var9 = handler.query(stmt, resultHandler);
    } finally {
      this.closeStatement(stmt);
    }

    return var9;
  }
}
```

### 优点

1. 将相同处理逻辑的代码放到抽象父类中，把不变的行为写在父类中，去除了子类的重复代码，提供了一个很好的代码复用平台，符合开闭原则。
2. 将不同的逻辑放到不同的子类中，通过子类的扩展增加新的行为，提高了代码的扩展性。
3. 通过父类调用子类的操作，通过对子类的扩展增加新的行为，实现了反向控制。

### 缺点

1. 每个抽象类都需要至少一个子类来实现，导致了类数量的增加。
2. 类数量增加间接增加了系统的复杂性。
3. 因为继承关系的自身缺点，如果父类添加一个新的抽象方法，所有子类都要实现一遍。