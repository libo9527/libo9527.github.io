---
title: Micro Major-Java Senior Architect
description: 微专业 - Java 高级开发工程师
comments: false
hidden: false
top: false
date: 2020-10-10 13:43:51
categories:
tags:
---

<img src="https://www.forknowledge.icu/gallery/thumbnails/javagcs.png" width="100%"/>

<!-- more -->

## 线程通信

### JDK API

#### ~~suspend / resume~~

调用 suspend 挂起目标线程，通过 resume 恢复线程。**已被废弃！**被弃用的主要原因是，**容易写出死锁的代码**。所以用 wait/notify 和 park/unpark 机制进行替代。

##### 死锁示例 1：在同步代码中使用

```java
/** 死锁的suspend/resume。 suspend并不会像wait一样释放锁，故此容易写出死锁代码 */
public void suspendResumeDeadLockTest() throws Exception {
  // 启动线程
  Thread consumerThread = new Thread(() -> {
    if (baozidian == null) { // 如果没包子，则进入等待
      System.out.println("1、进入等待");
      // 当前线程拿到锁，然后挂起
      synchronized (this) {
        Thread.currentThread().suspend();
      }
    }
    System.out.println("2、买到包子，回家");
  });
  consumerThread.start();
  // 3秒之后，生产一个包子
  Thread.sleep(3000L);
  baozidian = new Object();
  // 争取到锁以后，再恢复consumerThread
  synchronized (this) {
    consumerThread.resume();
  }
  System.out.println("3、通知消费者");
}
```

##### 死锁示例 2：suspend 在 resume 后执行

```java
/** 导致程序永久挂起的suspend/resume */
public void suspendResumeDeadLockTest2() throws Exception {
  // 启动线程
  Thread consumerThread = new Thread(() -> {
    if (baozidian == null) {
      System.out.println("1、没包子，进入等待");
      try { // 为这个线程加上一点延时
        Thread.sleep(5000L);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
      // 这里的挂起执行在resume后面
      Thread.currentThread().suspend();
    }
    System.out.println("2、买到包子，回家");
  });
  consumerThread.start();
  // 3秒之后，生产一个包子
  Thread.sleep(3000L);
  baozidian = new Object();
  consumerThread.resume();
  System.out.println("3、通知消费者");
  consumerThread.join();
}
```

#### wait / notify

wait 和 notify 方法只能由同一对象锁的持有者线程调用，也就是写在同步代码块里面，否则会抛出 aaa 异常。

- <span style="color: #2196F3;">wait</span> 方法导致当前线程等待，加入该对象的等待集合中，并且释放当前持有的对象锁。
- <span style="color: #2196F3;">notify/notifyAll</span>  方法唤醒一个或所有正在等待这个对象锁的线程。

<span style="color: #e91e63;">注意</span>：虽然 wait 会自动解锁，但是对顺序还是有要求，如果在 notify 被调用之后，才开始调用 wait，线程会永远处于 WAITING 状态。

##### 永久等待示例

```java
/** 会导致程序永久等待的wait/notify */
public void waitNotifyDeadLockTest() throws Exception {
  // 启动线程
  new Thread(() -> {
    if (baozidian == null) { // 如果没包子，则进入等待
      try {
        Thread.sleep(5000L);
      } catch (InterruptedException e1) {
        e1.printStackTrace();
      }
      synchronized (this) {
        try {
          System.out.println("1、进入等待");
          this.wait();
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
      }
    }
    System.out.println("2、买到包子，回家");
  }).start();
  // 3秒之后，生产一个包子
  Thread.sleep(3000L);
  baozidian = new Object();
  synchronized (this) {
    this.notifyAll();
    System.out.println("3、通知消费者");
  }
}
```

#### park / unpark

线程调用 park 则等待“许可”，调用 unpark 方法为指定线程提供“许可(permit)”。<span style="color: #2196F3;">不要求 park 和 unpack 方法的调用顺序</span>。

多次调用 unpack 后，再调用 park，线程会直接运行。但不会叠加，也就是说，连续多次调用 park 方法，第一次会拿到“许可”直接运行，后续调用会进入等待。“许可”类似于标志位。

##### 死锁示例

```java
/** 死锁的park/unpark */
public void parkUnparkDeadLockTest() throws Exception {
  // 启动线程
  Thread consumerThread = new Thread(() -> {
    if (baozidian == null) { // 如果没包子，则进入等待
      System.out.println("1、进入等待");
      // 当前线程拿到锁，然后挂起
      synchronized (this) {
        LockSupport.park();
      }
    }
    System.out.println("2、买到包子，回家");
  });
  consumerThread.start();
  // 3秒之后，生产一个包子
  Thread.sleep(3000L);
  baozidian = new Object();
  // 争取到锁以后，再恢复consumerThread
  synchronized (this) {
    LockSupport.unpark(consumerThread);
  }
  System.out.println("3、通知消费者");
}
```

#### 伪唤醒

<span style="color: #e91e63;">警告！之前代码中使用 if 语句来判断是否进入等待状态是错误的！</span>

官方建议<span style="color: #e91e63;">应该在循环中检查等待条件</span>，原因是处于等待状态的线程可能会收到<span style="color: #e91e63;">错误警报和伪唤醒</span>，如果不在循环中检查等待条件，程序就会在没有满足结束条件的情况下退出。

伪唤醒是指线程并非因为 notify、notifyAll、unpark 等 API 调用而唤醒，而是因为更底层原因导致的。

#### 总结

|                | 同步代码 | 代码顺序 |
| -------------- | -------- | -------- |
| suspend/resume | 死锁     | 永久挂起 |
| wait/notify    | 无       | 永久等待 |
| park/unpack    | 死锁     | 无       |

使用 while 循环代替 if 条件判断是否进入等待状态来避免伪唤醒。

## 线程封闭

通过将数据封闭在线程中而避免使用同步的技术称为**线程封闭**。

Java 中具体体现为 ThreadLocal、局部变量等。

### ThreadLocal

ThreadLocal 是 Java 里一种特殊的变量。

ThreadLocal 是线程级别变量，每个线程都有自己独立的一个变量，竞争条件被彻底消除了，在并发模式下是绝对安全的变量。

<span style="color: #2196F3;">可以理解为，JVM 维护了一个 Map\<Thread, T\>，每个线程要用这个 T 的时候，用当前的线程去 Map 里面取。</span>

##### 线程封闭示例

```java
/** 线程封闭示例 */
public class Demo7 {
  /** threadLocal变量，每个线程都有一个副本，互不干扰 */
  public static ThreadLocal<String> value = new ThreadLocal<>();

  /**
     * threadlocal测试
     *
     * @throws Exception
     */
  public void threadLocalTest() throws Exception {

    // threadlocal线程封闭示例
    value.set("这是主线程设置的123"); // 主线程设置值
    String v = value.get();
    System.out.println("线程1执行之前，主线程取到的值：" + v);

    new Thread(new Runnable() {
      @Override
      public void run() {
        String v = value.get();
        System.out.println("线程1取到的值：" + v);
        // 设置 threadLocal
        value.set("这是线程1设置的456");

        v = value.get();
        System.out.println("重新设置之后，线程1取到的值：" + v);
        System.out.println("线程1执行结束");
      }
    }).start();

    Thread.sleep(5000L); // 等待所有线程执行结束

    v = value.get();
    System.out.println("线程1执行之后，主线程取到的值：" + v);

  }

  public static void main(String[] args) throws Exception {
    new Demo7().threadLocalTest();
  }
}
```

### 栈封闭

<span style="color: #2196F3;">局部变量</span>的固有属性之一就是封闭在线程中。

## 线程池

线程是不是越多越好？为什么要用线程池？

1. 线程在 Java 中是一个对象，需要占用内存，更需要占用操作系统的资源，线程的创建、销毁都需要时间。如果创建时间 + 销毁时间 > 执行任务时间，就很不划算。
2. Java 对象占用堆内存，操作系统线程占用系统内存，根据 JVM 规范，一个线程默认最大栈大小为 1M，这个栈空间是需要从系统内存中分配的。线程过多，会消耗大量的内存。
3. 线程过多时，操作系统需要频繁切换线程上下文，影响性能。

<span style="color: #2196F3;">线程池的推出，就是为了方便的控制线程数量。</span>

### 概念

1. <span style="color: #2196F3;">线程池管理器</span>：用于创建并管理线程池，包括创建线程池，销毁线程池，添加新任务；
2. <span style="color: #2196F3;">工作线程</span>：线程池中的线程，在没有任务时处于等待状态，可以循环的执行任务；
3. <span style="color: #2196F3;">任务接口</span>：每个任务必须实现的接口，以供工作线程调度任务的执行，它主要规定了任务的入口，任务执行完后的收尾工作，任务的执行状态等；
4. <span style="color: #2196F3;">任务队列</span>：用于存放没有处理的任务，提供一种缓冲机制。

### API

#### 接口定义和实现类

| 类型   | 名称                        | 描述                                                         |
| ------ | --------------------------- | ------------------------------------------------------------ |
| 接口   | Executor                    | 最上层的接口，定义了<span style="color: #e91e63;">执行任务的方法 execute</span> |
| 接口   | ExecutorService             | 继承了 Executor 接口，拓展了 Callable、Future、关闭方法      |
| 接口   | ScheduledExecutorService    | 继承了 ExecutorService，增加了定时任务相关的方法             |
| 实现类 | ThreadPoolExecutor          | <span style="color: #e91e63;">基础、标准的线程池实现</span>  |
| 实现类 | ShceduledThreadPoolExecutor | 继承了 ThreadPoolExecutor，实现了 ScheduledExecutorService 中<span style="color: #e91e63;">定时任务</span>相关的方法 |

##### ExecutorService

```java
public interface ExecutorService extends Executor {

  // 监测 ExecutorService 是否已经关闭，直到所有任务执行完成，或发生超时，或当前线程被中断
  boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;

  // 执行给定的任务集合，执行完毕后，返回结果
  <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException;

  // 执行给定的任务集合，执行完毕或者超时后，返回结果，其他任务终止
  <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException;

  // 执行给定的任务，任意一个执行成功或者超时后，则返回结果，其他任务终止
  <T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException;

  // 如果此线程池已关闭，则返回 true
  boolean isShutdown();
  
  // 如果关闭后所有任务都已完成，则返回 true。
  boolean isTerminated();

  // 优雅关闭线程池，之前提交的任务将被执行，但是不会接受新的任务。
  void shutdown();
  
	// 尝试停止所有正在执行的任务，停止等待任务的处理，并返回等待执行任务的列表。
  List<Runnable> shutdownNow();
  
  // 提交一个用于执行的 Callable 返回任务，并返回一个 Future，用于获取 Callable 执行结果。
  <T> Future<T> submit(Callable<T> task);
  
  // 提交可运行任务以执行，并返回一个 Future 对象，执行结果为 null
  Future<?> submit(Runnable task);
  
  // 提交可运行任务以执行，并返回一个 Future 对象，执行结果为传入的 result
  <T> Future<T> submit(Runnable task, T result);
}
```

##### ScheduledExecutorService

创建并执行一个一次性任务，过了延迟时间就会被执行

```java
public <V> ScheduledFuture<V> schedule(Callable<V> callable, long delay, TimeUnit unit);

public ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit);
```

创建并执行一个周期性任务，过了给定的初始延迟时间，会第一次被执行，执行过程中发生了异常，那么任务就停止。

一次任务执行时长超过了周期时间，下一次任务会等到该次任务执行结束后<span style="color: #2196F3;">立即执行</span>，这也是它和 scheduleWithFixedDelay 的重要区别。

```java
public ScheduledFuture<?> scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit);
```

创建并执行一个周期性任务，过了给定的初始延迟时间，第一次被执行后，后续以给定的周期时间执行，执行过程中发生了异常，那么任务就停止。

一次任务执行时长超过了周期时间，下一次任务会等到该次任务执行结束的时间基础上，计算<span style="color: #2196F3;">执行延时</span>。

### Executors 工具类

- `public static ExecutorService newFixedThreadPool(int nThreads)`

  创建一个固定大小、任务队列容量无界的线程池。核心线程数=最大线程数。

- `public static ExecutorService newCachedThreadPool()`

  创建一个大小无界的缓冲线程池。它的任务队列是一个[同步队列](http://ifeve.com/java-synchronousqueue/)。任务加入到池中，如果池中有空闲线程，则用空闲线程执行，如无则创建新线程执行。池中的线程空闲超过 60 秒，将被销毁释放。线程数随任务的多少变化。适用于执行耗时较小的异步任务。池的核心线程数=0，最大线程数=Integer.MAX_VALUE

- `public static ScheduledExecutorService newSingleThreadScheduledExecutor()`

  只有一个线程来执行无界任务队列的单一线程池。该线程池确保任务按加入的顺序一个一个一次执行。当唯一的线程因任务异常中止时，将创建一个新的线程来继续执行后续的任务。与`newFixedThreadPool(1)`的区别在于，单一线程池的池大小在`newSingleThreadExecutor`方法中硬编码，不能再改变。

- `public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)`

  能定时执行任务的线程池。该池的核心线程数由参数指定，最大线程数=Interger.MAX_VALUE

### 任务执行过程

ThreadPoolExecutor.execute()

```java
public void execute(Runnable command) {
  if (command == null)
    throw new NullPointerException();
  /*
         * Proceed in 3 steps:
         *
         * 1. If fewer than corePoolSize threads are running, try to
         * start a new thread with the given command as its first
         * task.  The call to addWorker atomically checks runState and
         * workerCount, and so prevents false alarms that would add
         * threads when it shouldn't, by returning false.
         *
         * 2. If a task can be successfully queued, then we still need
         * to double-check whether we should have added a thread
         * (because existing ones died since last checking) or that
         * the pool shut down since entry into this method. So we
         * recheck state and if necessary roll back the enqueuing if
         * stopped, or start a new thread if there are none.
         *
         * 3. If we cannot queue task, then we try to add a new
         * thread.  If it fails, we know we are shut down or saturated
         * and so reject the task.
         */
  int c = ctl.get();
  if (workerCountOf(c) < corePoolSize) {
    if (addWorker(command, true))
      return;
    c = ctl.get();
  }
  if (isRunning(c) && workQueue.offer(command)) {
    int recheck = ctl.get();
    if (! isRunning(recheck) && remove(command))
      reject(command);
    else if (workerCountOf(recheck) == 0)
      addWorker(null, false);
  }
  else if (!addWorker(command, false))
    reject(command);
}
```

{% mermaid graph TD %}

A[执行] --> B{是否达到核心线程数}

B --> |否|D[创建新线程执行]

B --> |是|C{工作队列是否已满}

C --> |否|E[丢到队列里]

C --> |是|F{是否达到最大线程数量}

F --> |否|G[新建线程执行]

F --> |是|H[执行拒绝策略]

{% endmermaid %}

### 线程数量

<span style="color: #e91e63;">如何确定合适数量的线程？</span>

- <span style="color: #2196F3;">计算型任务</span>：CPU 数量的 1-2 倍
- <span style="color: #2196F3;">IO型任务</span>：相对计算型任务，需要多一些线程，要根据具体的 <span style="color: #2196F3;">IO 阻塞时长</span>进行考量决定。如 tomcat 中默认的最大线程数为 200。
- 也可以考虑根据需要在一个<span style="color: #2196F3;">最小数量和最大数量</span>间自动增减线程数。

## CAS 机制

CAS（Compare and swap 比较和交换）属于硬件同步原语，处理器提供了基本内存操作的原子性保证。

CAS 操作需要输入两个数值，一个旧值A和一个新值B，在操作期间先比较旧值有没有发生变化，如果没有发生变化，才交换新值，否则不交换。

Java 中的 sun.misc.Unsafe 类，提供了 compareAndSwapInt() 和 compareAndSwapIong() 等几个方法实现 CAS。

```java
package com.gzhennaxia.demo;

import sun.misc.Unsafe;

import java.lang.reflect.Field;

public class Demo {

  private volatile int value;
  private static Unsafe unsafe;
  private static long offset;

  static {
    try {
      Field field = Unsafe.class.getDeclaredField("theUnsafe");
      field.setAccessible(true);
      unsafe = (Unsafe) field.get(null);
      offset = unsafe.objectFieldOffset(Demo.class.getDeclaredField("value"));
    } catch (NoSuchFieldException | IllegalAccessException e) {
      e.printStackTrace();
    }
  }

  public void inc() {
    value++;
  }

  public void incCAS() {
    int current;
    do {
      current = unsafe.getIntVolatile(this, offset);
    } while (!unsafe.compareAndSwapInt(this, offset, current, current + 1));
  }

  public static void main(String[] args) throws InterruptedException {
    Demo demo10 = new Demo();
    for (int i = 0; i < 2; i++) {
      new Thread(() -> {
        for (int j = 0; j < 10000; j++) {
          //                    demo10.inc();
          demo10.incCAS();
        }
      }).start();
    }
    Thread.sleep(2000L);
    System.out.println(demo10.value);
  }
}
```

### J.U.C 包内的原子操作封装类

- AtomicBoolean：原子更新布尔类型
- AtomicInteger：原子更新整型
- AtomicLong：原子更新长整型



- AtomicIntegerArray：原子更新整型数组里的元素
- AtomicLongArray：原子更新长整形数组里的元素
- AtomicReferenceArray：原子更新引用类型数组里的元素



- AtomicIntegerFieldUpdater：原子更新整型字段的更新器
- AtomicLongFieldUpdater：原子更新长整形字段的更新器
- AtomicReferenceFieldUpdater：原子更新饮用类型的字段



- AtomicReference：原子更新引用类型
- AtomicStampedReference：原子更新带有版本号的引用类型
- AtomicMarkableReference：原子更新带有标记位的引用类型



1.8 更新：

更新器：DoubleAccumulator、LongAccumulator

计数器：DoubleAdder、LongAdder

计数器增强版，高并发下性能更好

频繁更新但不太频繁读取的汇总统计信息时使用

分成多个操作单元，不同线程更新不同的单元

只有需要汇总的时候才计算所有单元的操作

### CAS 的三个问题

1. 循环+CAS，自旋的实现让所有线程都处于高频运行，争抢 CPU 执行时间的状态。如果操作长时间不成功，会带来很大的 CPU 资源消耗。

2. 仅针对单个变量的操作，不能用于多个变量来实现原子操作。

3. [ABA 问题](https://www.cnblogs.com/senlinyang/p/7875381.html)

   线程1将A修改为B，线程2将B修改为C，线程3将B修改为A。

   如果执行顺序为线程1 -> 线程3 -> 线程2，则线程2将无法感知到线程1和线程3的修改。

## Java 锁

<span style="color: #2196F3;">自旋锁</span>：为了不放弃 CPU 执行事件，循环的使用 CAS 技术对数据进行更新，直至成功。



<span style="color: #2196F3;">悲观锁</span>：假定会发生并发冲突，同步所有对数据的相关操作，从读数据就开始上锁。

<span style="color: #2196F3;">乐观锁</span>：假定没有冲突，在修改数据时如果发现和之前获取的不一样，则读最新数据，修改后重试修改。

自旋锁就是乐观锁的一种体现。



<span style="color: #2196F3;">独享锁（写）</span>：给资源加上写锁，线程可以修改资源，其他线程不能再加锁；（单写）

<span style="color: #2196F3;">共享锁（读）</span>：给资源加上读锁后只能读不能改，其他线程也只能加读锁，不能加写锁；（多读）



<span style="color: #2196F3;">可重入锁、不可重入锁</span>：线程拿到一把锁之后，可以自由进入同一把锁所同步的其他代码。

<span style="color: #2196F3;">公平锁、非公平锁</span>：争抢锁的顺序，如果是按先来后到，则为公平。



几种重要的锁实现：synchronized、ReentrantLock、ReentrantReadWriteLock

### 同步关键字 synchronized

synchronized 属于最基本的线程通信机制，基于对象监视器实现。

Java 中的每个对象都与一个监视器相关联，一个线程可以锁定或解锁。

一次只有一个线程可以锁定监视器，试图锁定该监视器的任何其他线程都会被阻塞，直到它们可以获得该监视器上的锁定为止。

<span style="color: #2196F3;">特性</span>：可重入、独享、悲观锁

<span style="color: #2196F3;">锁的范围</span>：类锁、对象锁、锁消除、锁粗化

提示：同步关键字，不仅是实现同步，根据 JMM 规定，还能保证可见性(读取最新主内存数据，结束后写入主内存)。

#### 示例

##### 可重入

```java
// 可重入
public class ObjectSyncDemo2 {

  public synchronized void test1(Object arg) {
    System.out.println(Thread.currentThread() + " 我开始执行 " + arg);
    if (arg == null) {
      test1(new Object());
    }
    System.out.println(Thread.currentThread() + " 我执行结束" + arg);
  }

  public static void main(String[] args) throws InterruptedException {
    new ObjectSyncDemo2().test1(null);
  }
}
```

##### 锁粗化

```java
// 锁粗化(运行时 jit 编译优化)
// jit 编译后的汇编内容, jitwatch可视化工具进行查看
// just-in-time compilation，缩写为JIT；又译及时编译、实时编译
public class ObjectSyncDemo3 {
  int i;

  public void test1(Object arg) {
    synchronized (this) {
      i++;
    }
    synchronized (this) {
      i++;
    }
  }

  // 优化后只锁定一次
  //    public void test1(Object arg) {
  //        synchronized (this) {
  //            i++;
  //            i++;
  //        }
  //    }

  public static void main(String[] args) throws InterruptedException {
    for (int i = 0; i < 10000000; i++) {
      // 热点代码会进行分析后优化
      new ObjectSyncDemo3().test1("a");
    }
  }
}
```

##### 锁消除

```java
// 锁消除(jit)
public class ObjectSyncDemo4 {
  public void test3(Object arg) {
    StringBuilder builder = new StringBuilder();
    builder.append("a");
    builder.append(arg);
    builder.append("c");
    System.out.println(arg.toString());
  }

  public void test2(Object arg) {
    String a = "a";
    String c = "c";
    System.out.println(a + arg + c);
  }


  public void test1(Object arg) {
    // jit 优化, 消除了锁
    StringBuffer stringBuffer = new StringBuffer();
    stringBuffer.append("a");
    stringBuffer.append(arg);
    stringBuffer.append("c");
    // System.out.println(stringBuffer.toString());
  }

  public static void main(String[] args) throws InterruptedException {
    for (int i = 0; i < 1000000; i++) {
      new ObjectSyncDemo4().test1("123");
    }
  }
}
```

#### synchronized 加锁原理

> [死磕Synchronized底层实现--概论 - 掘金](https://juejin.im/post/6844903726545633287)
>
> Java中的`synchronized`有偏向锁、轻量级锁、重量级锁三种形式，分别对应了锁只被一个线程持有、不同线程交替持有锁、多线程竞争锁三种情况。当条件不满足时，锁会按偏向锁->轻量级锁->重量级锁的顺序升级。
>
> [The **Hotspot** Java Virtual Machine](https://www.cs.princeton.edu/picasso/mats/HotspotOverview.pdf)
>
> https://wiki.openjdk.java.net/display/hotspot/synchronization

### Lock 锁接口实现

#### Lock 核心API

| API               | 描述                                           |
| ----------------- | ---------------------------------------------- |
| lock              | 获取锁的方法，若锁被其他线程获取，则等待(阻塞) |
| lockInterruptibly | 在锁的获取过程中可以中断当前线程               |
| tryLock           | 尝试非阻塞地获取锁，立即返回                   |
| unlock            | 释放锁                                         |

提示：根据Lock接口的源码注释，Lock接口的实现，具备和同步关键字同样的内存语义。

### ReentrantLock

- 独享锁
- 支持公平、非公平两种模式
- 可重入

```java
// 可响应中断
public class LockInterruptiblyDemo1 {
  private Lock lock = new ReentrantLock();

  public static void main(String[] args) throws InterruptedException {
    LockInterruptiblyDemo1 demo1 = new LockInterruptiblyDemo1();
    Runnable runnable = new Runnable() {
      @Override
      public void run() {
        try {
          demo1.test(Thread.currentThread());
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
      }
    };
    Thread thread1 = new Thread(runnable);
    Thread thread2 = new Thread(runnable);
    thread1.start();
    Thread.sleep(500); // 等待0.5秒，让thread1先执行

    thread2.start();
    Thread.sleep(2000); // 两秒后，中断thread2

    thread2.interrupt();
  }

  public void test(Thread thread) throws InterruptedException {
    System.out.println(Thread.currentThread().getName() + "， 想获取锁");
    lock.lockInterruptibly();   //注意，如果需要正确中断等待锁的线程，必须将获取锁放在外面，然后将InterruptedException抛出
    try {
      System.out.println(thread.getName() + "得到了锁");
      Thread.sleep(10000); // 抢到锁，10秒不释放
    } finally {
      System.out.println(Thread.currentThread().getName() + "执行finally");
      lock.unlock();
      System.out.println(thread.getName() + "释放了锁");
    }
  }
}
```

#### ReadWriteLock

维护一对关联锁，一个用于只读操作，一个用于写入；读锁可以由多个读线程同时持有，写锁是排他的。

适合读取线程比写入线程多的场景，改进互斥锁的性能，示例场景：缓存组件、集合的并发线程安全性改造。

<span style="color: #2196F3;">锁降级</span>指的是写锁降级为读锁。把持住当前拥有的写锁的同时，再获取到读锁，随后释放写锁的过程。

写锁是线程独占，读锁是共享，所以写->读是降级。(无法实现读->写)

#### Codition

用于替代 wait/notify

Object 中的 wait()，notify()，notifyAll() 方法是和 synchronized 配合使用的，可以唤醒一个或者全部(单个等待集)；

Condition 是需要与 Lock 配合使用的，<span style="color: #2196F3;">提供了多个等待集合，更精确的控制</span>(底层是 park/unpark 机制)；

```java
// condition 实现队列线程安全。
public class QueueDemo {
  final Lock lock = new ReentrantLock();
  // 指定条件的等待 - 等待有空位
  final Condition notFull = lock.newCondition();
  // 指定条件的等待 - 等待不为空
  final Condition notEmpty = lock.newCondition();

  // 定义数组存储数据
  final Object[] items = new Object[100];
  int putptr, takeptr, count;

  // 写入数据的线程,写入进来
  public void put(Object x) throws InterruptedException {
    lock.lock();
    try {
      while (count == items.length) // 数据写满了
        notFull.await(); // 写入数据的线程,进入阻塞
      items[putptr] = x;
      if (++putptr == items.length) putptr = 0;
      ++count;
      notEmpty.signal(); // 唤醒指定的读取线程
    } finally {
      lock.unlock();
    }
  }
  // 读取数据的线程,调用take
  public Object take() throws InterruptedException {
    lock.lock();
    try {
      while (count == 0)
        notEmpty.await(); // 线程阻塞在这里,等待被唤醒
      Object x = items[takeptr];
      if (++takeptr == items.length) takeptr = 0;
      --count;
      notFull.signal(); // 通知写入数据的线程,告诉他们取走了数据,继续写入
      return x;
    } finally {
      lock.unlock();
    }
  }
}
```

## AQS 抽象队列同步器

> 运用到**模版方法设计模式**
>
> 即算法的整体骨架已确定，某些具体的步骤由子类去实现。

提供了对资源占用、释放，线程的等待、唤醒等等接口和具体实现。

可以用在各种需要控制资源争用的场景中。(ReentrantLock/CountDownLatch/Semphore)

![image-20201013163241377](/post_image/Jietu20201013-163450.png)

acquire、acquireShared：定义了资源争用的逻辑，如果没拿到，则等待。

<span style="color: #2196F3;">tryAcquire、tryAcquireShared：实际执行占用资源的操作，如何判定由使用者具体去实现。</span>

release、releaseShared：定义释放资源的逻辑，释放之后，通知后续节点进行争抢。

<span style="color: #2196F3;">tryRelease、tryReleaseShared：实际执行资源释放的操作，具体的AQS使用者去实现。</span>

![](/post_image/Jietu20201013-175221.png)

{% mermaid graph TD %}

A[acquire] --> B{tryAcquire}

B --> D[加入队尾]

B --> |抢到资源|C[end]

D --> E[寻找前置]

E --> F{前置节点是否为头节点}

F --> |是|G{tryAcquire}

F --> |否 park|H[等待]

H --> |unpark/interrupt|F

G --> |抢到资源|C

G --> |没抢到资源|E

{% endmermaid %}

### 信号量、栅栏和倒计时器

#### Semaphore

Semaphore 称为“信号量”，控制多个线程争抢许可。

- acquire：获取一个许可，如果没有就等待。
- release：释放一个许可。
- availablePermits：获取可用许可数目

典型场景：

- 代码并发处理限流(Hatrix)；

#### CountDownLatch

Java1.5 被引入的一个工具类，常被称为“倒计数器”。

创建对象时，传入指定数值作为线程参与的数量；

<span style="color: #2196F3;">await</span>：该方法等待计数器变为0，在这之前，线程进入等待状态；

<span style="color: #2196F3;">countdown</span>：计数器数值减一，知道为0；

经常用于等待其他线程执行到某一节点，再继续执行当前线程代码。

使用场景示例：

- 统计线程执行的情况；
- 压力测试中，使用 countDownLatch 实现最大程度的并发处理；
- 多个线程之间，相互通信，比如线程异步调用接口，结果通知；

```java
public class CountDownLatchDemo {

  public static void main(String[] args) {
    CountDownLatch countDownLatch = new CountDownLatch(10);
    for (int i = 0; i < 10; i++) {
      int finalI = i;
      new Thread(new Runnable() {
        @Override
        public void run() {
          try {
            Thread.sleep(2000);
          } catch (InterruptedException e) {
            e.printStackTrace();
          }
          System.out.println("线程" + finalI + "执行结束");
          countDownLatch.countDown();
        }
      }).start();
    }
    try {
      countDownLatch.await();
      System.out.println("主线程等待10个线程执行完后再往下执行");
    } catch (InterruptedException e) {
      e.printStackTrace();
    }

  }
}
```

#### CyclicBarrier

CyclicBarrier 也是1.5引入的，又称为“线程栅栏”。

创建对象时，指定栅栏线程数量。

<span style="color: #2196F3;">await</span>：等指定数量多线程都处于等待状态时，继续执行后续代码。

<span style="color: #2196F3;">barrierAction</span>：线程数量到了指定量后，自动触发执行指定任务。

和 CountDownLatch 的重要区别在于，CyclicBarrier 对象可以多次触发执行。

典型场景：

- 数据量比较大时，实现批量插入数据到数据库；
- 数据统计，30个线程统计30天数据，全部统计完毕后，执行汇总；

## 并发容器类

### Map

#### HashMap / ConcurrentHashMap 源码分析

> [探索 ConcurrentHashMap 高并发性的实现机制](https://developer.ibm.com/zh/articles/java-lo-concurrenthashmap/)
>
> [HashMap? ConcurrentHashMap? 相信看完这篇没人能难住你！](https://crossoverjie.top/2018/07/23/java-senior/ConcurrentHashMap/)
>
> [Java进阶（六）从ConcurrentHashMap的演进看Java多线程核心技术](http://www.jasongj.com/java/concurrenthashmap/)

#### ConcurrentSkipListMap

> [J.U.C 之ConcurrentSkipListMap - 掘金](https://juejin.im/post/6844903958499033095)
>
> [Skip list | 维基百科](https://en.wikipedia.org/wiki/Skip_list)

![](https://upload.wikimedia.org/wikipedia/commons/2/2c/Skip_list_add_element-en.gif)

特点：

- 有序链表实现
- 无锁实现(使用CAS)

- value 不能为空
- 层级越高，跳跃性越大，数据约少，速度越快
- 随机决定新节点是否抽出来作为索引
- 时间复杂度为 O(log n)，空间复杂度为 O(n)

### List

#### CopyOnWriteArrayList

CopyOnWriteArrayList 容器即写时复制容器，和 ArrayList 相比，优点是并发安全，缺点有两个：

1. 多了内存占用：写数据是 copy 一份完整的数据，单独进行操作。
2. 数据一致性：数据写完之后，其他线程不一定能马上读取到最新内容。

### Set

| 实现                  | 原理                       | 特点                   |
| --------------------- | -------------------------- | ---------------------- |
| HashSet               | 基于 HashMap 实现          | 非线程安全             |
| CopyOnWriteArraySet   | 基于 CopyOnWriteArrayList  | 线程安全               |
| ConcurrentSkipListSet | 基于 ConcurrentSkipListMap | 线程安全，有序，查询快 |

### Queue

| 方法    | 作用                     | 描述                                             |
| ------- | ------------------------ | ------------------------------------------------ |
| add     | 添加元素                 | 如果队列已满，则抛出 IllegalStateException 异常  |
| remove  | 移除并返回队列头部的元素 | 如果队列为空，则抛出 NoSuchElementException 异常 |
| element | 返回队列头部的元素       | 如果队列为空，则抛出 NoSuchElementException 异常 |
| offer   | 添加一个元素并返回 true  | 如果队列已满，则返回 false                       |
| poll    | 移除并返回队列头部的元素 | 如果队列为空，则返回 null                        |
| peek    | 返回队列头部的元素       | 如果队列为空，则返回 null                        |
| put     | 添加一个元素             | 如果队列已满，则阻塞                             |
| take    | 移除并返回队列头部的元素 | 如果队列为空，则阻塞                             |

## TCP/UDP 协议

### 传输控制协议 TCP

传输控制协议（TCP，Transmission Control Protocol）是一个传输层协议，提供面向连接的、可靠的、有序的、基于字节流的传输层通信协议。应用程序在使用 TCP 之前，必须先建立 TCP 连接。

#### TCP 三次握手

#### TCP 四次挥手

### 用户数据报协议 UDP

用户数据报协议属于传输层协议。提供无连接、不可靠、数据报尽力传输服务。