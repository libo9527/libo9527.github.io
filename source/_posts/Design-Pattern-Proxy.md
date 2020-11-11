---
title: Design Pattern - Proxy
description: 代理模式
comments: false
hidden: false
top: false
date: 2020-10-26 19:45:21
categories:
tags:
---

<img src="https://cdn.journaldev.com/wp-content/uploads/2013/07/proxy-design-pattern.jpg" width="100%"/>

<!-- more -->

## 代理模式

> [秒懂Java代理与动态代理模式](https://blog.csdn.net/shusheng0007/article/details/80864854)

用代理对象**代替**目标对象来实现某个目的（服务/主题/功能）。

### 目的

1. 隐藏目标对象
2. 增强目标对象

### 代理模式结构

> [代理模式 | **Refactoring.Guru**](https://refactoringguru.cn/design-patterns/proxy)

1. 服务接口

   声明了服务接口。 代理必须遵循该接口才能伪装成服务对象。

2. 服务类

   提供了一些实用的业务逻辑。

3. 代理类

   代理类包含一个指向服务对象的引用成员变量。 代理完成其任务 （例如延迟初始化、 记录日志、 访问控制和缓存等） 后会将请求传递给服务对象。 通常情况下， 代理会对其服务对象的整个生命周期进行管理。

4. 客户端

   客户端能通过同一接口与服务或代理进行交互，所以你可在一切需要服务对象的代码中使用代理。

## 静态代理

劳动仲裁案例：代理律师代理讨薪员工索要工资。

### 服务接口

> 代理主题：讨薪

```java
public interface AskAbility {

  public void askForPay();
}
```

### 服务类

```java
public class ZhangSan implements AskAbility {
  @Override
  public void askForPay() {
    System.out.println("还钱！");
  }
}
```

### 代理类

```java
public class Lawyer implements AskAbility {
  private ZhangSan zhangSan;

  public Lawyer(ZhangSan zhangSan) {
    this.zhangSan = zhangSan;
  }

  @Override
  public void askForPay() {
    zhangSan.askForPay();
    System.out.println("如果不还将承担法律责任！");
  }
}
```

### 客户端

```java
public class Client {

  public static void main(String[] args) {
    ZhangSan zhangSan = new ZhangSan();
    AskAbility ask = new Lawyer(zhangSan);
    ask.askForPay();
  }
}
```

## 动态代理

静态代理中代理类在编译期已经存在，一个服务类需要一个代理类与之对应，当服务类增多时，代理类随着增多，导致类数量太多。

动态代理就是为了减少类数量而产生。

动态代理有 JDK(基于接口、反射生成) 和 CGLIB(基于继承、字节码生成) 两种实现方式。

Spring AOP 就是基于动态代理实现的。

> [关于 Spring AOP (AspectJ) 你该知晓的一切](https://blog.csdn.net/javazejian/article/details/56267036)
>
> [Spring系列教材 （三）- 注解方式 IOC/DI](https://how2j.cn/k/spring/spring-annotation-ioc-di/1067.html)

### JDK 动态代理

两个重要的元素：

1. InvocationHandler 接口

   `public Object invoke(Object proxy, Method method, Object[] args) throws Throwable;`

   - proxy：动态代理对象
   - method：正在执行的方法
   - args：当前执行方法传入的实参

2. Proxy 类

   `public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) throws IllegalArgumentException`

```java
public class AskingHandler implements InvocationHandler {

  private Object target;

  public AskingHandler(Object target) {
    this.target = target;
  }

  @Override
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    Object invoke = method.invoke(target, args);
    System.out.println("如果不还将承担法律责任！");
    return invoke;
  }
}
```

```java
public class Client {

  public static void main(String[] args) {
    Employee employee = new Employee();
    InvocationHandler handler = new AskingHandler(employee);
    AskAbility proxy = (AskAbility) Proxy.newProxyInstance(employee.getClass().getClassLoader(), employee.getClass().getInterfaces(), handler);
    proxy.askForPay();
  }
}
```

### CGLIB 动态代理

JDK 只能对实现了接口的类做动态代理，而不能对没有实现接口的类做动态代理，所以出现了 cgLib。

CGLib（Code Generation Library）是一个强大、高性能的 Code 生成类库，它可以在程序运行期间动态扩展类或接口，它的底层是使用 **java字节码操作框架** ASM 实现。

CGLIB 两个重要元素：

1. MethodInterceptor 方法拦截器

   `Object intercept(Object var1, Method var2, Object[] var3, MethodProxy var4) throws Throwable;`

2. Enhancer

   ```java
   public static Object create(Class type, Callback callback) {
     Enhancer e = new Enhancer();
     e.setSuperclass(type);
     e.setCallback(callback);
     return e.create();
   }
   ```

#### 服务类

```java
public class Employee {

  public void askForPay() {
    System.out.println("还钱！");
  }
}
```

#### 方法拦截器

```java
public class AskingMethodInterceptor implements MethodInterceptor {

  @Override
  public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
    Object result = methodProxy.invokeSuper(o, objects);
    if (method.getName().equals("askForPay"))
      System.out.println("如果不还将承担法律责任！");
    return result;
  }
}
```

#### 客户端

```java
public class Client {

  public static void main(String[] args) {
    Employee employee = new Employee();
    Employee proxy = (Employee) Enhancer.create(employee.getClass(), new AskingMethodInterceptor());

    proxy.askForPay();
  }
}
```

