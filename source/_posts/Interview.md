---
title: Interview
description: 面试
comments: false
hidden: false
top: false
date: 2020-10-21 20:00:51
categories:
tags:
---

<img src="https://resources.workable.com/wp-content/uploads/2018/05/prepare-interviews-featured.png" width="100%"/>

<!-- more -->

## 编程思想

### OPP

面向过程编程（英语：Procedure-oriented Programming，缩写：OPP）

### OOP

面向对象编程（英语：Object-oriented programming，缩写：OOP）

#### OPP vs OOP

面向过程的程序设计<span style="color: red">把</span>计算机程序<span style="color: red">视为</span>一系列的**命令集合**，即一组函数的顺序执行。为了简化程序设计，面向过程把函数继续切分为子函数，即把大块函数通过切割成小块函数来降低系统的复杂度。

而面向对象的程序设计<span style="color: red">把</span>计算机程序<span style="color: red">视为</span>一组**对象的集合**，而每个对象都可以接收其他对象发过来的消息，并处理这些消息，**计算机程序的执行就是一系列消息在各个对象之间传递**。

#### 三大特征

面向对象的三大特征：封装、继承、多态

##### 封装

把数据以及对数据的操作封装在一个类里。

##### 继承

##### 多态

父类引用指向子类对象。

### AOP

在软件业，AOP 为 Aspect Oriented Programming 的缩写，意为：面向切面编程

## 数据结构

### B 树

### B+ 树

### 红黑树

## 算法

### 排序算法

### 动态规划

### 推荐算法

## Java 语言

### 基础

### 反射

### 序列化

### 泛型

### JVM

#### 类加载机制

##### 类的生命周期/类加载过程

类从被加载到虚拟机内存中开始，到卸载出内存为止，它的整个生命周期包括 <span style="color: red">7 个阶段</span>。其中准备、验证、解析 3 个部分统称为连接（Linking）。

加载(loading) --> 验证(Verification) -->准备(Preparation) --> 解析(Resolution) --> 初始化(Initialization) --> 使用(Using) --> 卸载(Unloading)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0f14bd8d34554d94a925fab4bbdc7911~tplv-k3u1fbpfcp-zoom-1.image)

###### 加载

将 `.class` 文件加载到 JVM 运行时数据区的**方法区**内，然后在堆中创建一个 java.lang.Class 对象，用来封装类在方法区内的数据结构。

`.class` 文件来源：

1. 本地磁盘
2. 网络下载
3. zip、jar 等归档文件中
4. 数据库
5. 动态编译 Java 源文件

###### 验证

验证的目的是为了确保 Class 文件中的字节流包含的信息符合当前虚拟机的要求，而且不会危害虚拟机自身的安全。

包括以下四个阶段的验证：

1. 文件格式的验证

   是否以 `0xCAFEBABE` 开头、主次版本号是否在当前虚拟机的处理范围之内、常量池中的常量是否有不被支持的类型。

2. 元数据的验证

   对字节码描述的信息进行语义分析。例如：这个类是否有父类，除了java.lang.Object 之外。

3. 字节码验证

   通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的。

4. 符号引用验证

###### 准备

为类的<span style="color: red">静态变量</span>分配内存，并根据数据类型将其初始化为默认的<span style="color: red">零值</span>(注意不是程序中设置的值)

<span style="color: red">注意</span>：如果是常量(static final)，且在声明时就指定了初始值，则会直接赋值为指定值。

###### 解析

将常量池中的符号引用转换为直接引用。

###### 初始化

初始化**类变量**

**类初始化时机**：

1. 创建类的实例，也就是 new 一个对象
2. 访问某个类或接口的静态变量
3. 调用类的静态方法
4. 反射 `Class.forName("")`
5. 初始化一个类的子类（会首先初始化子类的父类）
6. JVM 启动时标明的启动类

##### 类加载器

###### 分类

1. 启动类加载器

   负责加载 `JAVA_HOME\lib` 目录中能被虚拟机识别的类，可以通过 `Launcher.getBootstrapClassPath().getURLs()` 查看。

   由 C++ 实现，无法被 Java 程序直接引用。

2. 扩展类加载器

   负责加载 `JAVA_HOME\lib\ext` 目录下的类

3. 应用类加载器

   责加载用户类路径（ClassPath）所指定的类

###### 继承关系

通过组合的方式实现的继承关系。

- 启动类加载器没有父类

- 扩展类加载器的父类为 null

- 应用类加载器继承自扩展类加载器
- 自定义类加载器继承自引用类加载器

##### 双亲委派模型

当类加载器收到了类加载的请求，它首先把请求委托给父加载器去完成，当父加载器无法加载时，子加载器才会尝试自己去加载该类。

###### 意义

1. 防止重复加载
2. 保证 Java 基础类的安全性

###### 双亲委派模型的破坏者-线程上下文类加载器

在Java应用中存在着很多服务提供者接口（Service Provider Interface，SPI），这些接口允许第三方为它们提供实现，如常见的 SPI 有 JDBC、JNDI 等，这些 SPI 的接口属于 Java 核心库，一般存在 rt.jar 包中，由 Bootstrap 类加载器加载，而 SPI 的第三方实现代码则是作为Java应用所依赖的 jar 包被存放在 classpath 路径下，由于启动类加载器无法直接加载 SPI 的实现类，同时由于双亲委派模式的存在，Bootstrap 类加载器也无法反向委托AppClassLoader 加载器SPI的实现类。在这种情况下，我们就需要线程上下文类加载器。

线程上下文类加载器（contextClassLoader）可以通过`java.lang.Thread` 类中的`getContextClassLoader()` 和 `setContextClassLoader(ClassLoader cl)` 方法来获取和设置线程的上下文类加载器。如果没有手动设置上下文类加载器，线程将继承其父线程的上下文类加载器，初始线程的上下文类加载器是应用类加载器，在线程中运行的代码可以通过此类加载器来加载类和资源。

![](https://img-blog.csdn.net/20170625143404387?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamF2YXplamlhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

“破坏”是指破坏了父类加载器无法加载子类加载器范围的类这样的规则。

就是通过在父类加载器中使用 `Thread.getContextClassLoader()` 获取到子类加载器，然后再调用子类加载器的 `findClass()` 方法去加载。

### 集合框架

#### Collection

![](https://camo.githubusercontent.com/d1efb1abc3173aa2a607316dda79bea560fe333f/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313230383232303934383038342e706e67)

#### Map

![](https://camo.githubusercontent.com/bcc4885b038529bc66a48f637c14cc7933ce63da/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303230313130313233343333353833372e706e67)

##### HashMap

###### put 流程

![](https://upload-images.jianshu.io/upload_images/13947009-afee4ea47109f9c3.jpg)

1. 计算 key 的 hash 值(hashcode 高位抑或运算)
2. 判断哈希表是否为空，如果为空则进行 `resize()`
3. 根据 hash 值计算数组下标，查看数组对应位置是否有值，没有就直接创建新节点存入
4. 如果存在值则判断 hash 值是否相等、key 是否 equal，如果相等则覆盖旧值。
5. 如果不等，则判断是否是红黑树节点，如果是则进入红黑树 put 操作。
6. 如果不是则表示为链表，循环遍历链表进行插入操作
7. 插入后链表后，链表长度如果大于 8，而哈希表数组长度小于 64，则扩容后 reHash，数组长度大于 64，则将链表转化为红黑树
8. 最后 `++modCount`，`++size`，如果size 大与阀值则进行 `resize()`。

###### 哈希表数组长度为什么必须为 2 的幂次方？

为了通过减法结合按位与操作**代替**取模运算提高性能。

`a % b == (b-1) & a`

而只有当 b 是 2 的指数时，等式才成立。

###### 为什么要把 hashcode 与其高位进行抑或运算？

为了减少哈希碰撞。

### IO

四大基类：

1. `java.io.InputStream`
2. `java.io.OutputStream`
3. `java.io.Reader`
4. `java.io.Writer`

#### Files 和 Paths

从 Java 7 开始，提供了 Files 和 Paths 这两个工具类，能极大地方便我们读写文件。

虽然 Files 和 Paths 是 java.nio 包里面的类，但他俩封装了很多读写文件的简单方法。

### 多线程

## 设计模式

### [单例模式](https://libo9527.github.io/2020/10/22/Design-Pattern-Singleton/)

### 工厂模式

### 责任链模式

### 包装模式/装饰者模式

> 别称：包装模式、装饰器模式、Wrapper、Decorator

装饰器模式（Decorator Pattern）允许在**不改变其结构**的情况下向一个现有的对象添加新的功能。

#### 结构

1. **抽象构件** （Component）
2. **具体构件** （Concrete Component）
3. **抽象装饰类** （Decorator） 
4. **具体装饰类** （Concrete Decorators）

#### 简单示例

> [装饰器模式| 菜鸟教程](https://www.runoob.com/design-pattern/decorator-pattern.html)

画图形：图形有长方形、圆形；可以画红色的也可以画绿色的。

##### 抽象构件

```java
public interface Shape {
  void draw();
}
```

##### 具体构件

```java
public class Rectangle implements Shape {
  @Override
  public void draw() {
    System.out.print("Shape: rectangle\t");
  }
}
```

```java
public class Circle implements Shape {
  @Override
  public void draw() {
    System.out.print("Shape: circle\t");
  }
}
```

##### 抽象装饰类

```java
public abstract class ShapeDecorator implements Shape {
  protected Shape shape;

  public ShapeDecorator(Shape shape) {
    this.shape = shape;
  }
}
```

##### 具体装饰类

```java
public class RedShapeDecorator extends ShapeDecorator {
  public RedShapeDecorator(Shape shape) {
    super(shape);
  }

  @Override
  public void draw() {
    shape.draw();
    System.out.println("Color: red");
  }
}
```

```java
public class GreenShapeDecorator extends ShapeDecorator {
  public GreenShapeDecorator(Shape shape) {
    super(shape);
  }

  @Override
  public void draw() {
    shape.draw();
    System.out.println("Color: green");
  }
}
```

##### 客户端使用

```java
public static void main(String[] args) {
  Shape shape = new RedShapeDecorator(new Rectangle());
  shape.draw();

  shape = new GreenShapeDecorator(new Rectangle());
  shape.draw();

  shape = new RedShapeDecorator(new Circle());
  shape.draw();

  shape = new GreenShapeDecorator(new Circle());
  shape.draw();
}
```

```
Shape: rectangle	Color: red
Shape: rectangle	Color: green
Shape: circle	Color: red
Shape: circle	Color: green
```

#### 典型应用

> [设计模式| 装饰者模式及典型应用 - 掘金](https://juejin.im/post/6844903681322647566)

##### Java I/O

抽象构件：`java.io.InputStream`

具体构件：

- `java.io.FileInputStream`

- `java.io.ByteArrayInputStream`

- `java.io.PipedInputStream`

抽象装饰类：`java.io.FilterInputStream`

具体装饰类：

- `java.io.BufferedInputStream`

- `java.io.DataInputStream`

- `java.io.PushbackInputStream`

![](https://user-gold-cdn.xitu.io/2018/9/18/165ecd4e160d8682)

实例化一个具有缓存功能的字节流对象时，只需要在 FileInputStream 对象上再套一层 BufferedInputStream 对象即可。

```java
FileInputStream fileInputStream = new FileInputStream(filePath);
BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
```

DataInputStream 装饰者提供了对更多数据类型进行输入的操作，比如 int、double 等基本类型。

### 策略模式

### 模版方法模式

### 代理模式

### 迭代器模式

> [Design-Pattern-Iterator | libo9527](http://libo9527.github.io/2020/11/04/Design-Pattern-Iterator/)

#### 目的

在不暴露集合底层表现形式 （列表、 栈和树等） 的情况下遍历集合中所有的元素。

#### 主要思想

将集合的遍历行为抽取为单独的迭代器对象。

#### 结构

1. 抽象迭代器（Iterator）
2. 具体迭代器（Concrete Iterators）
3. 抽象集合（Collection）
4. 具体集合（Concrete Collections）

#### 简单示例

学生报数

##### 抽象迭代器

```java
public interface StudentIterator {
  boolean hashNext();

  Student next();
  
  int getPosition();
}
```

##### 具体迭代器

```java
public class ConcreteStudentIterator implements StudentIterator {

  private Student[] students;
  private int position = 0;

  public ConcreteStudentIterator(Student[] students) {
    this.students = students;
  }

  @Override
  public int getPosition() {
    return position;
  }

  @Override
  public boolean hashNext() {
    return position < students.length;
  }

  @Override
  public Student next() {
    return students[position++];
  }
}
```

##### 抽象集合

```java
public abstract class StudentCollection {

  abstract StudentIterator createIterator();
}
```

##### 具体集合

```java
public class ConcreteStudentCollection extends StudentCollection {
  private Student[] students;

  public ConcreteStudentCollection(Student[] students) {
    this.students = students;
  }

  @Override
  StudentIterator createIterator() {
    return new ConcreteStudentIterator(students);
  }
}
```

##### 客户端使用

```java
public static void main(String[] args) {
  StudentCollection studentCollection = new ConcreteStudentCollection(new Student[]{new Student("张三"), new Student("李四")});
  StudentIterator iterator = studentCollection.createIterator();
  while (iterator.hashNext()) {
    Student student = iterator.next();
    System.out.println("我是" + iterator.getPosition() + "号：" + student.getName());
  }
}
```

```
我是1号：张三
我是2号：李四

Process finished with exit code 0
```

#### 典型应用

##### Java 集合

![](../post_image/image_2020_11_04T11_06_19_787Z.png)

## 网络

### TCP

### HTTP

### HTTPS

## 数据库

### Mysql

### Oracle



### MongoDB

## Linux

## 框架

### Spring

#### IoC

#### AOP

### Spring Boot

#### Starter

Spring Boot 的 Starter 有两个作用：

1. 将某个功能/领域所需的依赖集中到一起，可以认为是一个组合依赖。

   例如 `spring-boot-starter-web` 就组合了`spring-web`、`spring-webmvc`、`spring-boot-starter-tomcat` 等依赖。

2. 提供自动配置类给 Spring 完成自动配置

#### 自动配置

Spring Boot 项目启动时会扫描所有所有依赖 jar 包下的 `spring.factories` 文件，将其中的自动配置类注册到 Spring IoC 容器中。

#### 启动流程



## 服务器

### Tomcat

### Nginx

## 缓存

### Redis

## 消息中间件

### RebbitMQ

### Kafka

## 容器

### Docker

## 操作系统

## 架构

### SSH

### SSM

### 微服务

### 分布式

### 集群

## 前端

### HTML

### CSS

### JS

### Vue



Redis 的持久化方式

Redis 有两种持久化：RDB（快照）和 AOF（追加文件）

注：4.0之后增加了混合方式，结合了 RDB 和 AOF 的优点。

区别：

- RDB 是将内存中数据某一时刻的快照写入二进制文件中。
- AOF 是将每条导致数据变动的指令追加到 aof 文件中。

优缺点：

|          | RDB  | AOF  |
| -------- | ---- | ---- |
| 文件大小 | 小   | 大   |
| 重启时间 | 短   | 长   |
| 数据丢失 | 多   | 少   |

