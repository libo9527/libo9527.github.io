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

CAS 只能作用在单个变量上，无法针对某段程序

- 当线程过多时，CAS失败增多，会导致CPU占用率过高