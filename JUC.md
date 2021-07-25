## 0、准备环境

+ 一个空的maven项目

+ 导入lombok依赖

  ```xml
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <version>1.18.8</version>
          </dependency>
  ```

+ 保证 jdk 是 1.8 版本

## 1、什么是JUC

```java
java.util //工具包、类
java.util.concurrent
java.util.concurrent.atomic
java.util.concurrent.locks
java.util.function
```

为什么使用：一些业务原生代码写不了

Runnable 没有返回值，效率相对 Callable 较低

## 2、线程和进程

进程：一个程序，QQ.exe，程序的集合，java 里的 jar 包

一个进程往往可以包含一到多个线程

java 默认有 2 个线程：main 和 GC

线程：开了一个进程 IDEA，写代码 + 自动保存（另一个线程负责）

对于 java 而言：Thread、Runnable、Callable

java 真的能开启线程吗？ 不能！

```java
// java.lang.Thread
	// Thread.start()
	public synchronized void start() {
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        group.add(this);

        boolean started = false;
        try {
            start0(); // 调用了 start0()
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }

	private native void start0(); // 是个本地方法，c写的，not java
```

java 无法直接操作硬件，是运行在 JVM 上的，所以无法开启线程。

并发（多个线程操作统一资源）：单核CPU，快速交替

并行（多个人一起行走）：多核CPU，多个线程同时执行

查看 CPU 核数

```java
System.out.println(Runtime.getRuntime().availableProcessors()); // 4
```

并发编程的本质：充分利用 CPU 的资源

线程有几个状态：6

```java
// java.lang.Thread
	public enum State {
        // 新生
        NEW,
        
        // 运行
        RUNNABLE,
        
        // 阻塞
        BLOCKED,
        
        // 等待，死死的等
        WAITING,
        
        // 超时等待
        TIMED_WAITING,
        
        // 终止
        TERMINATED;
    }
```

**wait 和 sleep**

1. 来自不同的类

   wait => Object

   sleep => Thread，一般不用这个，用下面这个

   ```java
   import java.util.concurrent.TimeUnit;
   
   TimeUnit.SECONDS.sleep(5);
   ```

2. 关于锁的释放

   wait 会释放锁，sleep 抱着锁睡觉

3. 使用的范围不同

   wait 必须在同步代码块中使用

   sleep 可以在任何地方使用

4. 是否需要捕获异常

   wait 不需要捕获异常，sleep 必须要捕获异常（怕发生超时等待）

## 3、Lock锁

传统 Synchronized

```java
// 基本的卖票例子
// 线程就是一个单独的资源类，没有任何附属的操作（属性+方法）
public class SaleTicketDemo01 {
    public static void main(String[] args) {
        // 并发：多线程操作同一个资源类，把资源类丢入线程
        Ticket ticket = new Ticket();

        // @FunctionalInterface 函数式接口
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 50; i++) {
                    ticket.sell();
                }
            }
        }, "A").start();
        // lambda表达式： (参数)->{代码}
        new Thread(()->{
            for (int i = 0; i < 50; i++) {
                ticket.sell();
            }
        }, "B").start();
        new Thread(()->{
            for (int i = 0; i < 50; i++) {
                ticket.sell();
            }
        }, "C").start();
    }
}

// 资源类 OOP
class Ticket {
    private int number = 50;

    public synchronized void sell(){
        if (number > 0){
            System.out.println(Thread.currentThread().getName() + "卖出了第" + number-- + "张票");
        }
    }
}
```

Lock 的 3 个实现类

ReentrantLock，ReentrantReadWriteLock.ReadLock，ReentrantReadWriteLock.WriteLock

```java
// java.util.concurrent.locks.ReentrantLock
	public ReentrantLock() {
        sync = new NonfairSync(); // 默认非公平锁
    }

	public ReentrantLock(boolean fair) { // 传入true，构造公平锁
        sync = fair ? new FairSync() : new NonfairSync();
    }
```

公平锁：先来后到排队

非公平锁：可以插队（默认）

Lock 锁的实现：

```java
public class SaleTicketDemo02 {
    public static void main(String[] args) {
        // 并发：多线程操作同一个资源类，把资源类丢入线程
        Ticket2 ticket = new Ticket2();

        // @FunctionalInterface 函数式接口
        new Thread(()->{for (int i = 0; i < 50; i++) ticket.sell();}, "A").start();
        new Thread(()->{for (int i = 0; i < 50; i++) ticket.sell();}, "B").start();
        new Thread(()->{for (int i = 0; i < 50; i++) ticket.sell();}, "C").start();
    }
}

// Lock
class Ticket2 {
    private int number = 100;

    Lock lock = new ReentrantLock();

    public void sell(){
        lock.lock(); // 加锁
        try {
            // 业务代码
            if (number > 0){
                System.out.println(Thread.currentThread().getName() + "卖出了第" + number-- + "张票");
            }
        } catch (Exception e){
            e.printStackTrace();
        } finally {
            lock.unlock(); // 解锁
        }
    }
}
```

synchronized 和 Lock 区别：

1. synchronized 是内置关键字，Lock 是一个 java 类
2. synchronized 无法判断获取锁的状态，Lock 可以
3. synchronized 会自动释放锁，Lock 必须要手动释放锁，不释放就死锁了
4. synchronized 没有超时等待，Lock 有 tryLock() 方法
5. synchronized 可重入锁，不可以中断，非公平；Lock 可重入锁，可以判断锁，可以公平和非公平（默认非公平）
6. synchronized 适合锁少量的代码，Lock 适合锁大量的同步代码

## 4、生产者和消费者问题

第一种写法：synchronized，wait，notify

```java
public class PC {
    public static void main(String[] args) {
        Data data = new Data();

        new Thread(()->{
            try {
                for (int i = 0; i < 100; i++) {
                    data.increment();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "A").start();
        new Thread(()->{
            try {
                for (int i = 0; i < 100; i++) {
                    data.decrement();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "B").start();
    }
}

// 判断等待 + 业务 + 通知
class Data{ // 资源类
    private int num = 0;

    // +1
    public synchronized void increment() throws InterruptedException {
        if (num != 0){
            // 等待
            this.wait();
        }
        num++;
        System.out.println(Thread.currentThread().getName() + " => " + num);
        // 通知其它线程
        this.notifyAll();
    }

    // -1
    public synchronized void decrement() throws InterruptedException {
        if (num == 0){
            // 等待
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName() + " => " + num);
        // 通知其它线程
        this.notifyAll();
    }
}
```

问题：如果有 4 个线程，就不能保证安全了，虚假唤醒

解决：把 if 判断改成 while 判断，防止虚假唤醒

第二种写法：用 Lock，JUC 版