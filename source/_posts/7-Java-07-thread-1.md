---
title: java多线程Thread
comments: true
date: 2020-07-23 20:30
categories: [Java]
tags: [Java]
---

## 创建多线程的方法
创建线程有几种方式有两种，一种是实现Runnable接口，另一种是继承Thread类。
前面的start方法中我们看到，run只是一个模板方法，本地方法start0中回去调用这个对应子类的run方法。其实这两种方式都是殊途同归。从实质上看，创建线程只有一种方式就是构造Thread类，创建线程执行单元的方式有两种，一种是重写Thread的run方法，另一种是实现Runnable接口的run方法，第二种方法将Runnab实例作为Thread的构造参数.

Thread到一个构造方法，实际是构造方法接受一个Runnable参数
```java
public Thread(Runnable target) {
    init(null, target, "Thread-" + nextThreadNum(), 0);
}
```

## join 
join某个线程A，会使得当前线程B 进入等待，直到线程A结束生命周期，或者到达指定时间，这个时候线程B是处于BLOCKED状态的，而不是A线程。

如果不使用join，主线程和thread0线程交替运行的情况，如果加入了thread0.join的方法，则主线程会等到thread0线程运行完成之后才开始运行
<!-- more -->
```java
public class TestJoin {
    public static void main(String[] args) throws InterruptedException {
        Thread thread0 = new Thread(() -> {
            IntStream.rangeClosed(0, 200).forEach(i -> System.out.println("thread-0-" + i));
        });

        thread0.start();
        // thread0 进程加入到 main 进程，会使得main进程进入等待，main线程 BLOCKED状态
        // 不使用 join 则 thread0和main线程交替进行
        thread0.join();
        IntStream.range(1, 200).forEach(i -> System.out.println("main-" + i));
    }
}
```

## sleep和yield
调用yield方法，会将当前线程从RUNNING状态切换到RUNNABLE状态，线程不会释放锁，只是提醒CPU本线程自愿放弃CPU的执行权，如果CPU资源不紧张，则会忽略这种提醒。而sleep方法则是会让当前线程进入阻塞状态，但是依旧不释放锁。

## interrupt 中断
以下thread0.interrupt()并不能使用线程正常退出，Thread.currentThread().isInterrupted()状态5s后会从false变为ture
```java
public class TestInterrupt {
    public static void main(String[] args) throws InterruptedException {
        Thread thread0 = new Thread(() -> {
            while (true) {
                System.out.println("this is thread-0 : " + Thread.currentThread().isInterrupted());
            }
        });

        thread0.start();
        TimeUnit.SECONDS.sleep(5);
        thread0.interrupt();
    }
}
```

通过自己设置标志位来优雅中断线程
```java
public class StopThreadGraceful extends Thread {
    public static void main(String[] args) throws InterruptedException {
        StopThreadGraceful t0 = new StopThreadGraceful();
        t0.start();
        Thread.sleep(10000L);
        // 关闭线程
        t0.close();
    }

    //设置自己的中断标志位
    private volatile boolean isClosed = false;

    @Override
    public void run() {
        while (true) {
            System.out.println("t1 will start work");
            while (!isClosed && !isInterrupted()) {//两者都判断
                // doing work;
                System.out.println("working...");
            }
            System.out.println("t1 will exit");
            break;
        }
    }

    //供外部调用的close方法，用于执行标记中断。
    public void close() {
        this.isInterrupted();
        this.isClosed = true;
    }
}
```

## 参考
- <https://blog.csdn.net/liman65727/article/details/106589424>
