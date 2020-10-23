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

> Static initializers are run by the JVM at class initialization time, after class loading but before the class is used by any thread. Because the <span style="color: red">JVM acquires a lock during initialization</span> [JLS 12.4.2] and this lock is acquired by each thread at least once to ensure that the class has been loaded, memory writes made during static initialization are automatically visible to all threads. Thus statically initialized objects require no explicit synchronization either during construction or when being referenced

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

类加载机制保障了饿汉模式的线程安全。JVM在类加载时会获取一把锁，这把锁在线程访问对象时也会先去争取，确保类已经被加载了。

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

#### 为什么要两次判空？

第一次判空是为了减少无谓的抢夺锁，提升销量。

第二次判空是为了保障不会破坏单例，假设没有第二次判空，线程1，线程2 同时通过第一次判空，之后线程1获得了锁，并实例化了 instance，之后线程2 获得锁，又会实例化一遍，破坏了单例模式。

#### 为什么要加 volatile 关键字？

> [彻头彻尾理解单例模式与多线程](https://blog.csdn.net/justloveyou_/article/details/64127789)

`instance = new Singleton4();` 可以被拆解为三条机器指令（伪代码）

```
memory = allocate();        //1:分配对象的内存空间
ctorInstance(memory);       //2:初始化对象
instance = memory;        //3:使 instance 引用指向刚分配的内存地址
```

由于存在指令重排序，上述三条指令的顺序可能会变成 1、3、2

如果没有使用 volatile，那假设线程1 已经执行到创建对象的语句了，且刚好执行到重排序后到机器指令3，此时 instance 已经不是 null 了，其他线程就会获取到一个没有完整初始化的 instance 对象，进而造成未知错误。

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

静态内部类模式综合了懒汉和饿汉模式，既满足线程安全，又满足延迟加载。

### 枚举单例模式

> 《Effective Java》作者认为该模式是单例模式的最佳实践。

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

反射攻击枚举单例模式

```java
public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
  Singleton6 instance = Singleton6.INSTANCE;

  Constructor<Singleton6> declaredConstructor = Singleton6.class.getDeclaredConstructor();
  declaredConstructor.setAccessible(true);
  Singleton6 instance2 = declaredConstructor.newInstance();
  System.out.println(instance == instance2);
}
```

```java
Exception in thread "main" java.lang.NoSuchMethodException: com.gzhennaxia.demo.singleton.Singleton6.<init>()
	at java.lang.Class.getConstructor0(Class.java:3082)
	at java.lang.Class.getDeclaredConstructor(Class.java:2178)
```

报错说找不到空构造方法，但即使手动添加了空构造方法同样会报这个错，因为 Java 编译器会为每个构造器自动添加两个参数，这点从反编译文件中可以看出（只有一个 `com/gzhennaxia/demo/singleton/Singleton6."<init>":(Ljava/lang/String;I)V` 双参构造器）。

```java
public enum Singleton6 {
  INSTANCE;

  private Singleton6() {
  }
}
```

```java
Classfile /Users/libo/Documents/GitHub/projects/demo/src/main/java/com/gzhennaxia/demo/singleton/Singleton6.class
  Last modified 2020年10月22日; size 921 bytes
  MD5 checksum bf867cdc275213b5182d9e511c593cad
  Compiled from "Singleton6.java"
public final class com.gzhennaxia.demo.singleton.Singleton6 extends java.lang.Enum<com.gzhennaxia.demo.singleton.Singleton6>
  minor version: 0
  major version: 56
  flags: (0x4031) ACC_PUBLIC, ACC_FINAL, ACC_SUPER, ACC_ENUM
  this_class: #4                          // com/gzhennaxia/demo/singleton/Singleton6
  super_class: #10                        // java/lang/Enum
  interfaces: 0, fields: 2, methods: 4, attributes: 2
Constant pool:
   #1 = Fieldref           #4.#29         // com/gzhennaxia/demo/singleton/Singleton6.$VALUES:[Lcom/gzhennaxia/demo/singleton/Singleton6;
   #2 = Methodref          #30.#31        // "[Lcom/gzhennaxia/demo/singleton/Singleton6;".clone:()Ljava/lang/Object;
   #3 = Class              #14            // "[Lcom/gzhennaxia/demo/singleton/Singleton6;"
   #4 = Class              #32            // com/gzhennaxia/demo/singleton/Singleton6
   #5 = Methodref          #10.#33        // java/lang/Enum.valueOf:(Ljava/lang/Class;Ljava/lang/String;)Ljava/lang/Enum;
   #6 = Methodref          #10.#34        // java/lang/Enum."<init>":(Ljava/lang/String;I)V
   #7 = String             #11            // INSTANCE
   #8 = Methodref          #4.#34         // com/gzhennaxia/demo/singleton/Singleton6."<init>":(Ljava/lang/String;I)V
   #9 = Fieldref           #4.#35         // com/gzhennaxia/demo/singleton/Singleton6.INSTANCE:Lcom/gzhennaxia/demo/singleton/Singleton6;
  #10 = Class              #36            // java/lang/Enum
  #11 = Utf8               INSTANCE
  #12 = Utf8               Lcom/gzhennaxia/demo/singleton/Singleton6;
  #13 = Utf8               $VALUES
  #14 = Utf8               [Lcom/gzhennaxia/demo/singleton/Singleton6;
  #15 = Utf8               values
  #16 = Utf8               ()[Lcom/gzhennaxia/demo/singleton/Singleton6;
  #17 = Utf8               Code
  #18 = Utf8               LineNumberTable
  #19 = Utf8               valueOf
  #20 = Utf8               (Ljava/lang/String;)Lcom/gzhennaxia/demo/singleton/Singleton6;
  #21 = Utf8               <init>
  #22 = Utf8               (Ljava/lang/String;I)V
  #23 = Utf8               Signature
  #24 = Utf8               ()V
  #25 = Utf8               <clinit>
  #26 = Utf8               Ljava/lang/Enum<Lcom/gzhennaxia/demo/singleton/Singleton6;>;
  #27 = Utf8               SourceFile
  #28 = Utf8               Singleton6.java
  #29 = NameAndType        #13:#14        // $VALUES:[Lcom/gzhennaxia/demo/singleton/Singleton6;
  #30 = Class              #14            // "[Lcom/gzhennaxia/demo/singleton/Singleton6;"
  #31 = NameAndType        #37:#38        // clone:()Ljava/lang/Object;
  #32 = Utf8               com/gzhennaxia/demo/singleton/Singleton6
  #33 = NameAndType        #19:#39        // valueOf:(Ljava/lang/Class;Ljava/lang/String;)Ljava/lang/Enum;
  #34 = NameAndType        #21:#22        // "<init>":(Ljava/lang/String;I)V
  #35 = NameAndType        #11:#12        // INSTANCE:Lcom/gzhennaxia/demo/singleton/Singleton6;
  #36 = Utf8               java/lang/Enum
  #37 = Utf8               clone
  #38 = Utf8               ()Ljava/lang/Object;
  #39 = Utf8               (Ljava/lang/Class;Ljava/lang/String;)Ljava/lang/Enum;
{
  public static final com.gzhennaxia.demo.singleton.Singleton6 INSTANCE;
    descriptor: Lcom/gzhennaxia/demo/singleton/Singleton6;
    flags: (0x4019) ACC_PUBLIC, ACC_STATIC, ACC_FINAL, ACC_ENUM

  public static com.gzhennaxia.demo.singleton.Singleton6[] values();
    descriptor: ()[Lcom/gzhennaxia/demo/singleton/Singleton6;
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=0, args_size=0
         0: getstatic     #1                  // Field $VALUES:[Lcom/gzhennaxia/demo/singleton/Singleton6;
         3: invokevirtual #2                  // Method "[Lcom/gzhennaxia/demo/singleton/Singleton6;".clone:()Ljava/lang/Object;
         6: checkcast     #3                  // class "[Lcom/gzhennaxia/demo/singleton/Singleton6;"
         9: areturn
      LineNumberTable:
        line 7: 0

  public static com.gzhennaxia.demo.singleton.Singleton6 valueOf(java.lang.String);
    descriptor: (Ljava/lang/String;)Lcom/gzhennaxia/demo/singleton/Singleton6;
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
         0: ldc           #4                  // class com/gzhennaxia/demo/singleton/Singleton6
         2: aload_0
         3: invokestatic  #5                  // Method java/lang/Enum.valueOf:(Ljava/lang/Class;Ljava/lang/String;)Ljava/lang/Enum;
         6: checkcast     #4                  // class com/gzhennaxia/demo/singleton/Singleton6
         9: areturn
      LineNumberTable:
        line 7: 0

  static {};
    descriptor: ()V
    flags: (0x0008) ACC_STATIC
    Code:
      stack=4, locals=0, args_size=0
         0: new           #4                  // class com/gzhennaxia/demo/singleton/Singleton6
         3: dup
         4: ldc           #7                  // String INSTANCE
         6: iconst_0
         7: invokespecial #8                  // Method "<init>":(Ljava/lang/String;I)V
        10: putstatic     #9                  // Field INSTANCE:Lcom/gzhennaxia/demo/singleton/Singleton6;
        13: iconst_1
        14: anewarray     #4                  // class com/gzhennaxia/demo/singleton/Singleton6
        17: dup
        18: iconst_0
        19: getstatic     #9                  // Field INSTANCE:Lcom/gzhennaxia/demo/singleton/Singleton6;
        22: aastore
        23: putstatic     #1                  // Field $VALUES:[Lcom/gzhennaxia/demo/singleton/Singleton6;
        26: return
      LineNumberTable:
        line 8: 0
        line 7: 13
}
Signature: #26                          // Ljava/lang/Enum<Lcom/gzhennaxia/demo/singleton/Singleton6;>;
SourceFile: "Singleton6.java"
```

从反编译结果可以看出枚举的实例化调用的是继承自 `java.lang.Enum` 的 `protected Enum(String var1, int var2)` 构造器，那是否可以反射调用该构造器来实例化 Singleton6 呢？

```java
public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
  Singleton6 instance = Singleton6.INSTANCE;

  Constructor<Singleton6> declaredConstructor = Singleton6.class.getDeclaredConstructor(String.class, int.class);
  declaredConstructor.setAccessible(true);
  Singleton6 instance2 = declaredConstructor.newInstance("", 0);
  System.out.println(instance == instance2);
}
```

```
Exception in thread "main" java.lang.IllegalArgumentException: Cannot reflectively create enum objects
	at java.lang.reflect.Constructor.newInstance(Constructor.java:417)
```

结果报错，查看 java.lang.reflect.Constructor.newInstance(Constructor.java:417)

```java
public T newInstance(Object ... initargs)
  throws InstantiationException, IllegalAccessException,
IllegalArgumentException, InvocationTargetException
{
  if (!override) {
    if (!Reflection.quickCheckMemberAccess(clazz, modifiers)) {
      Class<?> caller = Reflection.getCallerClass();
      checkAccess(caller, clazz, null, modifiers);
    }
  }
  if ((clazz.getModifiers() & Modifier.ENUM) != 0)
    ////////////  417  //////////// 
    throw new IllegalArgumentException("Cannot reflectively create enum objects");
  ConstructorAccessor ca = constructorAccessor;   // read volatile
  if (ca == null) {
    ca = acquireConstructorAccessor();
  }
  @SuppressWarnings("unchecked")
  T inst = (T) ca.newInstance(initargs);
  return inst;
}
```

可以看到反射创建实例的时候会判断类的修饰符中是否有 enum，如果有就抛出 `IllegalArgumentException` 异常。

因此，**枚举类型可以防止反射攻击**。

### 非枚举的防守方法

在构造器中判断实例是否已经存在，存在就抛出异常，保证构造器只被调用一次！

```java
public class Singleton1 {

  private static Singleton1 instance = new Singleton1();

  private Singleton1() {
    if (instance != null)
      throw new RuntimeException("实例已存在，单例构造器只能被调用一次！");
  }

  public static Singleton1 getInstance() {
    return instance;
  }
}
```

## 序列化攻击

一般类需要实现 `Serializable` 接口才能被序列化，但枚举类不用，因为枚举类本质是继承了 `java.lang.Enum` 的，而 Enum 已经声明实现了 `Serializable`。

一般的单例模式无法防止序列化攻击，经过序列化和反序列化后会创建出新的实例。

```java
public class SingletonTest2 {

  public static void main(String[] args) throws IOException, ClassNotFoundException {
    Singleton1 instance = Singleton1.getInstance();

    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
    objectOutputStream.writeObject(instance);
    byte[] bytes = outputStream.toByteArray();

    ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(bytes);
    ObjectInputStream objectInputStream = new ObjectInputStream(byteArrayInputStream);
    Singleton1 instance1 = (Singleton1) objectInputStream.readObject();

    System.out.println(instance == instance1);
  }
}
```

```
false

Process finished with exit code 0
```

但是对于枚举类来说，反序列化后还是原来的实例。

```java
public static void main(String[] args) throws IOException, ClassNotFoundException {
  Singleton6 instance = Singleton6.INSTANCE;

  ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
  ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
  objectOutputStream.writeObject(instance);
  byte[] bytes = outputStream.toByteArray();

  ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(bytes);
  ObjectInputStream objectInputStream = new ObjectInputStream(byteArrayInputStream);
  Singleton6 instance1 = (Singleton6) objectInputStream.readObject();

  System.out.println(instance == instance1);
}
```

```
true

Process finished with exit code 0
```

可以看到反序列化枚举后得到的实例和序列化之前的实例是同一个实例。查看 `ByteArrayOutputStream.writeObject()` 方法源码：

```java
public final void writeObject(Object obj) throws IOException {
  if (enableOverride) {
    writeObjectOverride(obj);
    return;
  }
  try {
    writeObject0(obj, false);
  } catch (IOException ex) {
    if (depth == 0) {
      writeFatalException(ex);
    }
    throw ex;
  }
}
```

`writeObject0()` 源码：

```java
private void writeObject0(Object obj, boolean unshared)
  throws IOException
{

  //  ...

  // remaining cases
  if (obj instanceof String) {
    writeString((String) obj, unshared);
  } else if (cl.isArray()) {
    writeArray(obj, desc, unshared);
  } else if (obj instanceof Enum) {
    writeEnum((Enum<?>) obj, desc, unshared);
  } else if (obj instanceof Serializable) {
    writeOrdinaryObject(obj, desc, unshared);
  } else {
    if (extendedDebugInfo) {
      throw new NotSerializableException(
        cl.getName() + "\n" + debugInfoStack.toString());
    } else {
      throw new NotSerializableException(cl.getName());
    }
  }
} finally {
  depth--;
  bout.setBlockDataMode(oldMode);
}
}
```

如果是枚举类型，会调用 `writeEnum()` 方法：

```java
private void writeEnum(Enum<?> en,
                       ObjectStreamClass desc,
                       boolean unshared)
  throws IOException
{
  /**
  * Enum 类型标记：129
     * new Enum constant.
     * @since 1.5
     */
  // final static byte TC_ENUM =         (byte)0x7E;
  bout.writeByte(TC_ENUM);
  ObjectStreamClass sdesc = desc.getSuperDesc();
  writeClassDesc((sdesc.forClass() == Enum.class) ? desc : sdesc, false);
  handles.assign(unshared ? null : en);
  writeString(en.name(), false);
}
```

接着再看 Enum 类型的反序列化，`ObjectInputStream.readObject()` 源码：

```java
public final Object readObject()
  throws IOException, ClassNotFoundException
{
  if (enableOverride) {
    return readObjectOverride();
  }

  // if nested read, passHandle contains handle of enclosing object
  int outerHandle = passHandle;
  try {
    Object obj = readObject0(false);
    handles.markDependency(outerHandle, passHandle);
    ClassNotFoundException ex = handles.lookupException(passHandle);
    if (ex != null) {
      throw ex;
    }
    if (depth == 0) {
      vlist.doCallbacks();
    }
    return obj;
  } finally {
    passHandle = outerHandle;
    if (closed && depth == 0) {
      clear();
    }
  }
}
```

`ObjectInputStream.readObject0()` 源码：

```java
private Object readObject0(boolean unshared) throws IOException {
  // ...

  try {
    switch (tc) {

        // ...
      case TC_ENUM:
        return checkResolve(readEnum(unshared));

      case TC_OBJECT:
        return checkResolve(readOrdinaryObject(unshared));

      case TC_EXCEPTION:
        IOException ex = readFatalException();
        throw new WriteAbortedException("writing aborted", ex);

      case TC_BLOCKDATA:
      case TC_BLOCKDATALONG:
        if (oldMode) {
          bin.setBlockDataMode(true);
          bin.peek();             // force header read
          throw new OptionalDataException(
            bin.currentBlockRemaining());
        } else {
          throw new StreamCorruptedException(
            "unexpected block data");
        }

      case TC_ENDBLOCKDATA:
        if (oldMode) {
          throw new OptionalDataException(true);
        } else {
          throw new StreamCorruptedException(
            "unexpected end of block data");
        }

      default:
        throw new StreamCorruptedException(
          String.format("invalid type code: %02X", tc));
    }
  } finally {
    depth--;
    bin.setBlockDataMode(oldMode);
  }
}
```

`ObjectInputStream.readEnum()` 源码：

```java
private Enum<?> readEnum(boolean unshared) throws IOException {

  // ...
  if (cl != null) {
    try {
      @SuppressWarnings("unchecked")
      Enum<?> en = Enum.valueOf((Class)cl, name);
      result = en;
    } catch (IllegalArgumentException ex) {
      throw (IOException) new InvalidObjectException(
        "enum constant " + name + " does not exist in " +
        cl).initCause(ex);
    }
    if (!unshared) {
      handles.setObject(enumHandle, result);
    }
  }

  handles.finish(enumHandle);
  passHandle = enumHandle;
  return result;
}
```

`Enum.valueOf()` 源码：

```java
public static <T extends Enum<T>> T valueOf(Class<T> enumType,
                                            String name) {
  T result = enumType.enumConstantDirectory().get(name);
  if (result != null)
    return result;
  if (name == null)
    throw new NullPointerException("Name is null");
  throw new IllegalArgumentException(
    "No enum constant " + enumType.getCanonicalName() + "." + name);
}
```

最终是根据 `name` 在枚举类实力数组里查找，所以返回的实例是已经存在的实例，并不会新建实例。

因此**枚举可以防止反序列化攻击**。

### 非枚举的防守方法

增加 `readResolve()` 方法返回单例，反序列化时会判断对象是否存在该方法，存在则会调用该方法返回对象。

```java

```



## 总结

|                              | 饿汉模式                            | 懒汉模式                            | 线程安全的懒汉模式                  | 双重校验锁模式                      | 静态内部类单例模式                  | 枚举单例模式                        |
| ---------------------------- | ----------------------------------- | ----------------------------------- | ----------------------------------- | ----------------------------------- | ----------------------------------- | ----------------------------------- |
| 延迟加载                     | <span style="color: red"/>否</span> | 是                                  | 是                                  | 是                                  | 是                                  | <span style="color: red"/>否</span> |
| 线程安全                     | 是                                  | <span style="color: red"/>否</span> | 是                                  | 是                                  | 是                                  | 是                                  |
| 反射攻击<br />(能否抵御)     | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | 能                                  |
| 反序列化攻击<br />(能否抵御) | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | <span style="color: red"/>否</span> | 能                                  |

