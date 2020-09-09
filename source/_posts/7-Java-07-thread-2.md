---
title: java多线程2-多线程相关关键字
comments: true
date: 2020-07-23 20:30
categories: [Java]
tags: [Java]
---

## notify和wait

## synchronized

## wait和sleep的区别
这个结果的区别很明显，通过sleep方法实现的暂停，程序是顺序进入同步块的，只有当上一个线程执行完成的时候，下一个线程才能进入同步方法，sleep暂停期间一直持有monitor对象锁，其他线程是不能进入的。而wait方法则不同，当调用wait方法后，当前线程会释放持有的monitor对象锁，因此，其他线程还可以进入到同步方法，线程被唤醒后，需要竞争锁，获取到锁之后再继续执行。

不同点：

1、wait是属于Object对象的方法，而sleep是属于Thread的静态方法

2、wait必须在同步代码块或者同步方法中调用，而sleep没有这个限制

3、调用sleep的线程会在短暂休眠之后会主动退出阻塞，而调用wait方法的线程需要被其他线程唤醒才能退出阻塞

4、调用sleep的线程进入休眠的时候并不会释放对应的锁，而调用wait的方法会释放对应的锁

相同点：

1、都是可中断方法

2、都可以使线程进入阻塞状态


## 参考
- <https://blog.csdn.net/liman65727/article/details/106804828>