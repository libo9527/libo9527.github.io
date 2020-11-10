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

> [Snailclimb](https://github.com/Snailclimb)/[JavaGuide](https://github.com/Snailclimb/JavaGuide)
>
> [CyC2018](https://github.com/CyC2018)/[CS-Notes](https://github.com/CyC2018/CS-Notes)
>
> [《Java 面试进阶指南》](https://xiaozhuanlan.com/javainterview?rel=javaguide) 

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

B树（balance tree）可以认为是 m 叉的多路平衡查找树



### B+ 树

#### B树和B+树的区别

> [b树和b+树的区别](https://blog.csdn.net/login_sonata/article/details/75268075)

- B+树的中间节点不保存数据，只在叶子结点中保存数据；而B树数据分布在整颗树中。
- 因为B+树的叶子结点按顺序链接，可以很方便的进行**范围查找**。

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

#### 索引

oracle 默认的索引是 **B+树**索引

##### 索引结构

###### B+ 树索引

![](http://hongyitong.github.io/img/B%E6%A0%91%E7%B4%A2%E5%BC%95.jpg)

###### 位图索引

> [位图索引:原理（BitMap index）——浅显易懂](https://blog.csdn.net/qq_24236769/article/details/75801687)

![](http://hongyitong.github.io/img/%E4%BD%8D%E5%9B%BE%E7%B4%A2%E5%BC%95.jpg)

位图索引适合只有几个**固定值**的列，如性别、婚姻状况、行政区等。

位图可以通过 AND/OR 操作，快速得到查询结果。

此外，位图索引适合**静态**数据，而不适合索引频繁更新的列。

因为位图索引列的修改会将所有该值的行进行加锁。

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

> [Redis | JavaGuide](https://github.com/Snailclimb/JavaGuide/blob/master/docs/database/Redis/redis-all.md)

#### 单线程

虽然说 Redis 是单线程模型，但是， 实际上，Redis 在 4.0 之后的版本中就已经加入了对多线程的支持。

不过，Redis 4.0 增加的多线程主要是针对一些**大键值对的删除操作**的命令，使用这些命令就会使用主处理之外的其他线程来“异步处理”。

大体上来说，Redis 6.0 之前主要还是单线程处理。

Redis6.0 引入多线程主要是为了提高**网络 IO** 读写性能，因为这个算是 Redis 中的一个性能瓶颈（Redis 的瓶颈主要受限于内存和网络）。

##### 为什么 Redis 选择单线程模型

> [为什么 Redis 选择单线程模型](https://draveness.me/whys-the-design-redis-single-thread/)

Redis 选择使用单线程模型处理客户端的请求主要还是因为 **CPU 不是 Redis 服务器的瓶颈**，所以**使用多线程模型带来的性能提升并不能抵消它带来的开发成本和维护成本**，系统的性能瓶颈也主要在网络 I/O 操作上；

而 Redis 引入多线程操作也是出于性能上的考虑，对于一些大键值对的删除操作，通过多线程非阻塞地释放内存空间也能减少对 Redis 主线程阻塞的时间，提高执行的效率。

#### 内存淘汰机制

##### Redis是如何判断数据是否过期？

Redis 通过一个叫做**过期字典**（可以看作是hash表）来保存数据过期的时间。过期字典的键指向Redis数据库中的某个key(键)，过期字典的值是一个long long类型的整数，这个整数保存了key所指向的数据库键的过期时间（毫秒精度的UNIX时间戳）。

##### Redis 内存淘汰机制

Redis 提供 6 种数据淘汰策略：

1. **volatile-lru（least recently used）**：从已设置过期时间的数据集中挑选最近最少使用的数据淘汰
2. **volatile-ttl**：从已设置过期时间的数据集中挑选将要过期的数据淘汰
3. **volatile-random**：从已设置过期时间的数据集中任意选择数据淘汰
4. **allkeys-lru（least recently used）**：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 key（这个是<span style="color: red">最常用</span>的）
5. **allkeys-random**：从数据集中任意选择数据淘汰
6. **no-eviction**：禁止驱逐数据，也就是说当内存不足以容纳新写入数据时，新写入操作会报错。这个应该没人使用吧！

4.0 版本后增加以下两种：

1. **volatile-lfu（least frequently used）**：从已设置过期时间的数据集(server.db[i].expires)中挑选最不经常使用的数据淘汰
2. **allkeys-lfu（least frequently used）**：当内存不足以容纳新写入数据时，在键空间中，移除最不经常使用的 key

#### 持久化

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

#### 事务

Redis 可以通过 MULTI，EXEC，DISCARD 和 WATCH 等命令来实现事务(transaction)功能。

使用 MULTI命令后可以输入多个命令。Redis不会立即执行这些命令，而是将它们放到队列，当调用了EXEC命令将执行所有命令。

但是，Redis 的事务和我们平时理解的关系型数据库的事务不同。

Redis 是**不支持 roll back** 的，因而不满足原子性的（而且不满足持久性）。

#### 缓存穿透

##### 什么是缓存穿透？

缓存穿透说简单点就是大量请求的 key 根本不存在于缓存中，导致请求直接到了数据库上，根本没有经过缓存这一层。

##### 解决办法

1. 参数校验

   一些不合法的参数请求直接抛出异常信息返回给客户端。

2. 缓存无效 key

   如果缓存和数据库都查不到某个 key 的数据就写一个固定值(e.g. null)到 Redis 中去并设置过期时间

3. 布隆过滤器

   把所有可能存在的请求的值都存放在布隆过滤器中，当用户请求过来，先判断用户发来的请求的值是否存在于布隆过滤器中。不存在的话，直接返回请求参数错误信息给客户端，存在的话才会继续走下面的流程。

   计算元素值的哈希值。根据哈希值，在位数组中把对应下标的值置为 1。

#### 缓存雪崩

##### 什么是缓存雪崩？

缓存在同一时间大面积的失效，后面的请求都直接落到了数据库上，造成数据库短时间内承受大量请求。

##### 解决办法

**针对 Redis 服务不可用的情况：**

1. 采用 Redis 集群，避免单机出现问题整个缓存服务都没办法使用。
2. 限流，避免同时处理大量的请求。

**针对热点缓存失效的情况：**

1. 合理设置缓存过期时间(e.g. 随机)

2. 分布式缓存

   为了防止缓存服务器宕机出现的缓存雪崩，可以使用分布式缓存，分布式缓存中每一个节点只缓存部分的数据，当某个节点宕机时可以保证其它节点的缓存仍然可用。

3. 缓存预热

   避免在系统刚启动不久由于还未将大量数据进行缓存而导致缓存雪崩。

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

