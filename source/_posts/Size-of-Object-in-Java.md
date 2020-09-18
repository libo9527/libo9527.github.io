---
title: Size of Object in Java
description: 对象的大小
comments: false
hidden: false
top: false
date: 2020-09-17 14:29:54
categories:
tags:
---

<img src="cover" width="100%"/>

<!-- more -->

> [如何计算Java对象所占内存的大小](https://www.jianshu.com/p/9d729c9c94c4)

## Java 对象内存结构

在HotSpot虚拟机里，对象在堆内存中的存储布局可以划分为三个部分：对象头（Header）、实例数据（Instance Data）和对齐填充（Padding）。

### 对象头

HotSpot 虚拟机对象的对象头部分包括两类信息。第一类是用于存储对象自身的运行时数据，如哈希码（HashCode）、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳等，这部分数据的长度在32位和64位的虚拟机（未开启压缩指针）中分别为 32 个比特和 64 个比特，官方称它 为“Mark Word”。

对象头的另外一部分是类型指针（kclass），即对象指向它的类型元数据的指针，Java虚拟机通过这个指针来确定该对象是哪个类的实例。如果对象是一个Java数组，那在对象头中还必须有一块用于记录数组长度的数据。同样，这部分数据的长度在 32 位和 64 位的虚拟机（未开启指针压缩）中分别为32个比特和64个比特。

#### 指针压缩

从 JDK 1.6 update14 开始，64 bit JVM 正式支持了 `-XX:+UseCompressedOops` 这个可以压缩指针，起到节约内存占用的新参数。

所以，当开启指针压缩后，对象头的大小为：

32位虚拟机对象头大小= Mark Word（4B）+ kclass(4B) = 8B

64位虚拟机对象头大小= Mark Word（8B）+ kclass(4B) = 12B

### 实例数据

8 种基本类型和 reference 大小在虚拟机上都是固定的,见下表

| Primitive Type | Memory Required(bytes) |
| -------------- | ---------------------- |
| boolean        | 1                      |
| byte           | 1                      |
| short          | 2                      |
| char           | 2                      |
| int            | 4                      |
| float          | 4                      |
| long           | 8                      |
| double         | 8                      |
| Reference      | 4                      |

### 对齐填充

由于 HotSpot 虚拟机的自动内存管理系统要求对象起始地址必须是 8 字节的整数倍，换句话说就是任何对象的大小都必须是8字节的整数倍。对象头部分已经被精心设计成正好是8字节的倍数（1倍或者2倍），因此，如果对象实例数据部分没有对齐的话，就需要通过对齐填充来补全。

## 使用 Instrumentation 获取对象大小

> [一个对象占用多少字节？](https://www.iteye.com/blog/yueyemaitian-2033046)
>
> https://juejin.im/post/6844904022101475342

`Instrumentation` 是 Java SE 5 引入的特性，通过构建一个独立于应用程序的代理程序（Agent），使用 `Instrumentation` 来监测和协助运行在 JVM 上的程序，甚至能够实现字节码修改。简单的说，`Instrumentation` 实现了一个虚拟机层面的 AOP 。

这个类的唯一实现类 InstrumentationImpl 的构造方法是私有的，在创建时，需要依赖一个 nativeAgent，和运行环境所支持的一些预定义类信息，我们在代码中无法直接实例化它，需要在 JVM 启动时，通过指定代理的方式，让 JVM 来实例化它。

使用 `Instrumentation` 的 `getObjectSize()` 方法获取对象大小。

`Instrumentation` 作为代理程序，使用虚拟机参数 javaagent 来指定。

### `Instrumentation` 的使用

1. 创建代理类

   ```java
   import java.lang.instrument.Instrumentation;
   
   public class ObjectShallowSize {
   
       private static Instrumentation inst;
   
       public static void premain(String agentArgs, Instrumentation instP) {
           inst = instP;
       }
   
       public static long sizeOf(Object o) {
           return inst.getObjectSize(o);
       }
   }
   ```

2. 打包

   在 ObjectShallowSize.java 路径下新建 /META-INF/MANIFEST.MF 指定 Premain-Class 内容为：

   ```MF
   Manifest-Version: 1.0
   Premain-Class: ObjectShallowSize
   ```

   编译 ObjectShallowSize.java

   ```bash
   javac ObjectShallowSize.java
   ```

   打包

   ```bash
   jar cvfm java-agent-sizeof.jar META-INF/MANIFEST.MF .
   ```

3. 测试

   创建测试类：

   ```java
   public class ObjectSizeTest {
   
       public static void main(String[] args) {
           System.out.println(ObjectShallowSize.sizeOf(new ObjectSizeTest()));
       }
   }
   ```

   编译测试类

   ```bash
   javac ObjectSizeTest.java
   ```

   运行测试类

   ```bash
   java -javaagent:java-agent-sizeof.jar ObjectSizeTest
   ```

![image-20200917150706508](/post_image/image-20200917150706508.png)

理论值：Mark Word（8B）+ kclass(4B) + Padding(4B) = `16B`

实际值：`16B`

#### 添加实例变量

```java
public class ObjectSizeTest {

    private int i;

    public static void main(String[] args) {
        System.out.println(ObjectShallowSize.sizeOf(new ObjectSizeTest()));
    }
}
```

理论值：Mark Word（8B）+ kclass(4B) + i(4B) = `16B`

实际值:`16B`

```java
public class ObjectSizeTest {

    private int i;
    private int j;

    public static void main(String[] args) {
        System.out.println(ObjectShallowSize.sizeOf(new ObjectSizeTest()));
    }
}
```

理论值：Mark Word（8B）+ kclass(4B) + i(4B) + j(4B)+Padding(4B) = `24B`

实际值：`24B`

```
public class ObjectSizeTest {

    private int i;
    private int j;
    private String s;
    private boolean aBoolean;
    private char c;
    

    public static void main(String[] args) {
        System.out.println(ObjectShallowSize.sizeOf(new ObjectSizeTest()));
    }
}
复制代码
```

理论值：Mark Word（8B）+ kclass(4B) + i(4B) + j(4B) + aBoolean(1B) + c(2B) + Paddding(1B) + s(4B) + Paddding(4B) = `32B`

实际值：`32B`

```
public class ObjectSizeTest {

    private String s;
    private int i1;
    private byte b1;
    private byte b2;
    private int i2;
    private Object obj;
    private byte b3;

    public static void main(String[] args) {
        System.out.println(ObjectShallowSize.sizeOf(new ObjectSizeTest()));
    }
}
复制代码
```

理论值：Mark Word（8B）+ kclass(4B) + s(4B) + i1(4B) + b1(1B) + b2(1B) +  2(padding) + i2(4B) + obj(4B)+ b3(1B) + Paddding(7B) = `40B`

实际值：`32B`

HotSpot虚拟机中，实例字段的存储顺序会受到虚拟机分配策略参数（`-XX：FieldsAllocationStyle` 参数）和字段在 Java 源码中定义顺序的影响。
HotSpot 虚拟机默认的分配顺序为 longs/doubles、ints、shorts/chars、bytes/booleans、oops（Ordinary Object Pointers，OOPs），从以上默认的分配策略中可以看到，相同宽度的字段总是被分配到一起存
放，在满足这个前提条件的情况下，在父类中定义的变量会出现在子类之前。如果HotSpot虚拟机的 `+XX：CompactFields` 参数值为true（默认就为true），那子类之中较窄的变量也允许插入父类变量的空隙之中，以节省出一点点空间。

因此上述情况下，内存分配情况为：

理论值：Mark Word（8B）+ kclass(4B) + i1(4B) + i2(4B) + b1(1B) + b2(1B) + b3(1B) + Paddding(1B) + s(4B) + obj(4B) = `32B`

实际值：`32B`

对于实例变量为对象的，实际应该通过反射机制取出 Java 对象中实例变量，递归计算累加出实际大小。

```java
public class ObjectShallowSize {

    private static Instrumentation inst;

    /**
     * @param agentArgs 是 premain 函数得到的程序参数，随同 “– javaagent”一起传入。
     *                  与 main 函数不同的是，这个参数是一个字符串而不是一个字符串数组，如果程序参数有多个，程序将自行解析这个字符串。
     * @param instP     是一个 java.lang.instrument.Instrumentation 的实例，由 JVM 自动传入。
     * @author bli@skystartrade.com
     * @date 2020-09-17 11:54
     */
    public static void premain(String agentArgs, Instrumentation instP) {
        inst = instP;
    }

    /**
     * 直接计算当前对象占用空间大小，包括
     * 当前类及超类的基本类型实例字段大小、
     * 引用类型实例字段引用大小、
     * 实例基本类型数组总占用空间、
     * 实例引用类型数组引用本身占用空间大小
     * <br>
     * 但是不包括超类继承下来的和当前类声明的实例引用字段的对象本身的大小、
     * 实例引用数组引用的对象本身的大小
     *
     * @author bli@skystartrade.com
     * @date 2020-09-17 17:09
     */
    public static long sizeOf(Object o) {
        return inst.getObjectSize(o);
    }

    /**
     * 递归计算当前对象占用空间总大小，包括当前类和超类的实例字段大小以及实例字段引用对象大小
     *
     * @author bli@skystartrade.com
     * @date 2020-09-17 17:12
     */
    public static long fullSizeOf(Object objP) throws IllegalAccessException {
        Set<Object> visited = new HashSet<Object>();
        // 双端队列（Double Ended Queue）
        Deque<Object> toBeQueue = new ArrayDeque<Object>();
        toBeQueue.add(objP);
        long size = 0L;
        while (toBeQueue.size() > 0) {
            Object obj = toBeQueue.poll();
            // sizeOf的时候已经计基本类型和引用的长度，包括数组
            size += skipObject(visited, obj) ? 0L : sizeOf(obj);
            Class<?> tmpObjClass = obj.getClass();
            if (tmpObjClass.isArray()) {
                // 基本类型名字长度是2，例如 int[] -> "[I"
                if (tmpObjClass.getName().length() > 2) {
                    for (int i = 0, len = Array.getLength(obj); i < len; i++) {
                        Object tmp = Array.get(obj, i);
                        if (tmp != null) {
                            // 非基本类型需要深度遍历其对象
                            toBeQueue.add(Array.get(obj, i));
                        }
                    }
                }
            } else {
                while (tmpObjClass != null) {
                    Field[] fields = tmpObjClass.getDeclaredFields();
                    for (Field field : fields) {
                        if (Modifier.isStatic(field.getModifiers())   //静态不计
                                || field.getType().isPrimitive()) {    //基本类型不重复计
                            continue;
                        }

                        field.setAccessible(true);
                        Object fieldValue = field.get(obj);
                        if (fieldValue == null) {
                            continue;
                        }
                        toBeQueue.add(fieldValue);
                    }
                    tmpObjClass = tmpObjClass.getSuperclass();
                }
            }
        }
        return size;
    }

    /**
     * String.intern的对象不计；
     * 计算过的不计，也避免死循环
     *
     * @author bli@skystartrade.com
     * @date 2020-09-17 17:15
     */
    private static boolean skipObject(Set<Object> visited, Object obj) {
        if (obj instanceof String && obj == ((String) obj).intern()) {
            return true;
        }
        return visited.contains(obj);
    }

}
```

