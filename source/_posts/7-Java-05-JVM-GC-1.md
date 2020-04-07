---
title: JVM GC简单理解（1）
comments: true
date: 2020-03-25 10:57
categories: [Java]
tags: [Java]
---


## windows/mac 下调试java性能分析
>  jvisualvm可视化工具的使用(需要使用管理员权限运行 jdk/bin/jvisualvm.exe) <https://www.cnblogs.com/baihuitestsoftware/articles/6477680.html>

- `jvisualvm` GC插件查看新老生代内存情况
- 使用`jvisualvm`工具获取到java进程pid
- `jstack pid`

- `jstat -gcutil {pid} 1200` 查看gc回收情况 

- 一些分析命令
```sh
> ps -aux | grep 'java'
> jps -l # 查看启动的java进程pid
> jcmd -l # 查看启动的java进程pid
> jcmd 26313 VM.flags # 查看进程启动参数
> jstack 25943 
> jmap 25943 
> jmap -heap 25943 
> jmap -histo 25943 | less
> jstat -gcutil 25943 2s
> cat /home/admin/service_gc.log | grep -i 'Full'
```

## GC

### 哪些内存需要GC
- JVM中，程序计数器、虚拟机栈、本地方法栈都是随线程而生随线程而灭，栈帧随着方法的进入和退出做入栈和出栈操作，实现了自动的内存清理,无需GC
    它们在编译期便知道内存占用大小，内存的分配和回收都具有确定性
- JVM中线程共享的Heap 区、Method Area,这部分内存是运行期间动态创建的，内存的分配和回收具有不确定性，是GC重点关注的


### GC的分类
- Minor GC(Young GC): 发生在新生代的垃圾收集工作。新生代几乎是所有对象出生的地方（当然存在例外，如果对象内存分配的时候发现新生代空间不够的时候会将对象直接分配在老年区）
- Full GC(Major GC): 发生在老年代的垃圾收集动作。没有Minor GC那么频繁。且耗时比Minor GC要久的多。

### 两种GC的触发条件
- Minor GC：
新生代GC的触发情况很简单，就是当在新生代Eden或者某个Survivor区创建对象内存不够的时候，就会尝试Minor GC。
- Full GC:
  - old空间不足：如果Eden区不足以分配足够的内存给即将创建的大对象，那么大对象会在Old区创建。此时如果Old区内存也不足，那么就会触发Full GC
  - Young区晋升到Old区的空间不足：在进行Young GC之前，会判断这次Young GC是否安全，这里所谓的安全是当前老年代的剩余空间可以容纳之前 Young GC晋升对象的平均大小，或者可以容纳Young的全部对象，如果结果是不安全的，就不会执行这次 Young GC，转而执行一次Full GC
  - PermGen Space（全称是Permanent Generation space,是指内存的永久保存区域）空间不足
  - 执行 System.gc()、jmap -histo:live 、jmap -dump
  - Young GC出现Promotion Faliure：当对象的GC年龄达到阈值时，或者To区放不下时，会把该对象复制到 Old区，如果Old区空间不足时，会发生Promotion Faliure，并接下去触发Full GC

<!-- more -->
## GC 日志解析
> gc日志在线分析工具 <https://gceasy.io/>
### Minor GC
```log
2020-04-01T21:18:46.892+0800: 99909.658: [GC (Allocation Failure) 2020-04-01T21:18:46.893+0800: 99909.658: [ParNew: 1751064K->73218K(1887488K), 0.1116726 secs] 1751064K->73218K(3984640K), 0.1120676 secs] [Times: user=0.38 sys=0.00, real=0.11 secs] 
```

- `2020-04-01T21:18:46.892+0800` GC发生的时间
- `99909.658` GC开始，相对JVM启动的相对时间，单位是秒
- `GC` 区别MinorGC和FullGC的标识，这次代表的是MinorGC
- `(Allocation Failure)` MinorGC的原因，在这个case里边，由于年轻代不满足申请的空间，因此触发了MinorGC
- `ParNew`  收集器的名称
- `1751064K->73218K` 收集前后年轻代的使用情况
- `(1887488K)` 整个年轻代的容量
- `0.1116726 secs` 这个解释用原滋原味的解释：Duration for the collection w/o final cleanup
- `1751064K->73218K` 收集前后整个堆的使用情况 ???
- `(3984640K)` 整个堆的容量
- `0.1120676 secs` ParNew收集器标记和复制年轻代活着的对象所花费的时间（包括和老年代通信的开销、对象晋升到老年代时间、垃圾收集周期结束一些最后的清理对象等的花销）
- `[Times: user=0.38 sys=0.00, real=0.11 secs]` GC事件在不同维度的耗时，具体的用英文解释起来更加合理:
user – Total CPU time that was consumed by Garbage Collector threads during this collection
sys – Time spent in OS calls or waiting for system event
real – Clock time for which your application was stopped. With Parallel GC this number should be close to (user time + system time) divided by the number of threads used by the Garbage Collector. In this particular case 8 threads were used. Note that due to some activities not being parallelizable, it always exceeds the ratio by a certain amount.

### Full/Major GC 
```log
2020-04-01T11:16:58.549+0800: 63801.314: [GC (CMS Initial Mark) [1 CMS-initial-mark: 0K(2097152K)] 1426009K(3984640K), 1.0288212 secs] [Times: user=4.06 sys=0.00, real=1.02 secs] 
2020-04-01T11:16:59.578+0800: 63802.343: [CMS-concurrent-mark-start]
2020-04-01T11:16:59.616+0800: 63802.381: [CMS-concurrent-mark: 0.037/0.037 secs] [Times: user=0.04 sys=0.00, real=0.04 secs] 
2020-04-01T11:16:59.616+0800: 63802.381: [CMS-concurrent-preclean-start]
2020-04-01T11:16:59.625+0800: 63802.390: [CMS-concurrent-preclean: 0.009/0.009 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
2020-04-01T11:16:59.625+0800: 63802.390: [CMS-concurrent-abortable-preclean-start]
 CMS: abort preclean due to time 2020-04-01T11:17:04.842+0800: 63807.607: [CMS-concurrent-abortable-preclean: 4.484/5.217 secs] [Times: user=4.51 sys=0.00, real=5.22 secs] 
2020-04-01T11:17:04.843+0800: 63807.608: [GC (CMS Final Remark) [YG occupancy: 1426009 K (1887488 K)]2020-04-01T11:17:04.843+0800: 63807.608: [GC (CMS Final Remark) 2020-04-01T11:17:04.843+0800: 63807.608: [ParNew: 1426009K->81173K(1887488K), 0.0793281 secs] 1426009K->81173K(3984640K), 0.0795787 secs] [Times: user=0.27 sys=0.00, real=0.08 secs] 
2020-04-01T11:17:04.922+0800: 63807.688: [Rescan (parallel) , 0.0504123 secs]2020-04-01T11:17:04.973+0800: 63807.738: [weak refs processing, 0.0000368 secs]2020-04-01T11:17:04.973+0800: 63807.738: [class unloading, 0.0669748 secs]2020-04-01T11:17:05.040+0800: 63807.805: [scrub symbol table, 0.0354613 secs]2020-04-01T11:17:05.076+0800: 63807.841: [scrub string table, 0.0016133 secs][1 CMS-remark: 0K(2097152K)] 81173K(3984640K), 0.2423149 secs] [Times: user=0.58 sys=0.00, real=0.24 secs] 
2020-04-01T11:17:05.085+0800: 63807.851: [CMS-concurrent-sweep-start]
2020-04-01T11:17:05.085+0800: 63807.851: [CMS-concurrent-sweep: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2020-04-01T11:17:05.085+0800: 63807.851: [CMS-concurrent-reset-start]
2020-04-01T11:17:05.089+0800: 63807.855: [CMS-concurrent-reset: 0.004/0.004 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
```


过程分析(垃圾回收器CMS的各个阶段)： 
1. `Initial Mark`: 初始标记（CMS的第一个STW阶段），标记GC Root直接引用的对象。它有两个目标：一是标记老年代中所有的GC Roots；二是标记被年轻代中活着的对象引用的对象。
`2020-04-01T11:16:58.549+0800: 63801.314: [GC (CMS Initial Mark) [1 CMS-initial-mark: 0K(2097152K)] 1426009K(3984640K), 1.0288212 secs] [Times: user=4.06 sys=0.00, real=1.02 secs]`
    -  `GC (CMS Initial Mark)` 收集阶段  
    -  `[1 CMS-initial-mark: 0K(2097152K)]`  老年代使用0 K???  ()中表示老年代总容量
    -  `1426009K(3984640K)` 整个堆使用情况  和（堆总容量）  
    -  `1.0288212 secs] [Times: user=4.06 sys=0.00, real=1.02 secs]`  时间计量

2. `Concurrent-mark`:  并发标记阶段, 这个阶段会遍历整个老年代并且标记所有存活的对象，从“初始化标记”阶段找到的GC Roots开始。并发标记的特点是和应用程序线程同时运行。并不是老年代的所有存活对象都会被标记，因为标记的同时应用程序会改变一些对象的引用等。
`2020-04-01T11:16:59.616+0800: 63802.381: [CMS-concurrent-mark: 0.037/0.037 secs] [Times: user=0.04 sys=0.00, real=0.04 secs] `
    -  `CMS-concurrent-mark`  并发收集阶段，这个阶段会遍历整个年老代并且标记活着的对象；
    -  `0.037/0.037 secs` 展示该阶段持续的时间和时钟时间  
    -  `[Times: user=0.04 sys=0.00, real=0.04 secs]`  时间计量 

3.  `Concurrent-preclean`: 并发预清理阶段, 这个阶段又是一个并发阶段，和应用线程并行运行，不会中断他们。前一个阶段在并行运行的时候，一些对象的引用已经发生了变化，当这些引用发生变化的时候，JVM会标记堆的这个区域为Dirty Card(包含被标记但是改变了的对象，被认为"dirty")，这就是 Card Marking。

4.  `Concurrent-abortable-preclean`:  并发可中止的预清理阶段, 又一个并发阶段不会停止应用程序线程。这个阶段尝试着去承担STW的Final Remark阶段足够多的工作。这个阶段持续的时间依赖好多的因素，由于这个阶段是重复的做相同的事情直到发生aboart的条件（比如：重复的次数、多少量的工作、持续的时间等等）之一才会停止。

5.  `Final Remark` : 重标记阶段（CMS的第二个STW阶段), 这个阶段是CMS中第二个并且是最后一个STW的阶段。该阶段的任务是完成标记整个年老代的所有的存活对象。由于之前的预处理是并发的，它可能跟不上应用程序改变的速度，这个时候，STW是非常需要的来完成这个严酷考验的阶段。
```log
2020-04-01T11:17:04.843+0800: 63807.608: [GC (CMS Final Remark) [YG occupancy: 1426009 K (1887488 K)]2020-04-01T11:17:04.843+0800: 63807.608: [GC (CMS Final Remark) 
2020-04-01T11:17:04.843+0800: 63807.608: [ParNew: 1426009K->81173K(1887488K), 0.0793281 secs] 1426009K->81173K(3984640K), 0.0795787 secs] [Times: user=0.27 sys=0.00, real=0.08 secs] 
2020-04-01T11:17:04.922+0800: 63807.688: [Rescan (parallel) , 0.0504123 secs]2020-04-01T11:17:04.973+0800: 63807.738: [weak refs processing, 0.0000368 secs]2020-04-01T11:17:04.973+0800: 63807.738: [class unloading, 0.0669748 secs]2020-04-01T11:17:05.040+0800: 63807.805: [scrub symbol table, 0.0354613 secs]2020-04-01T11:17:05.076+0800: 63807.841: [scrub string table, 0.0016133 secs][1 CMS-remark: 0K(2097152K)] 81173K(3984640K), 0.2423149 secs] [Times: user=0.58 sys=0.00, real=0.24 secs] 
```

通常CMS尽量运行Final Remark阶段在年轻代是足够干净的时候，目的是消除紧接着的连续的几个STW阶段。 
  - `(CMS Final Remark)` 收集阶段，这个阶段会标记老年代全部的存活对象，包括那些在并发标记阶段更改的或者新创建的引用对象
  - `[YG occupancy: 1426009 K (1887488 K)]` 年轻代当前占用情况和容量
  - `[Rescan (parallel) , 0.0504123 secs]` 这个阶段在应用停止的阶段完成存活对象的标记工作
  - `[weak refs processing, 0.0000368 secs]` 第一个子阶段，随着这个阶段的进行处理弱引用
  - `[class unloading, 0.0669748 secs]` 第二个子阶段(that is unloading the unused classes, with the duration and timestamp of the phase);
  - `[scrub string table, 0.0016133 secs]` 最后一个子阶段（that is cleaning up symbol and string tables which hold class-level metadata and internalized string respectively）
  - `[1 CMS-remark: 0K(2097152K)]` 在这个阶段之后老年代占有的内存大小和老年代的容量
  - `81173K(3984640K)` 在这个阶段之后整个堆的内存大小和整个堆的容量

6.  `Concurrent-sweep`: ，并发清理, 和应用线程同时进行，不需要STW。这个阶段的目的就是移除那些不用的对象，回收他们占用的空间并且为将来使用。

7.  `Concurrent-reset`: 这个阶段并发执行，重新设置CMS算法内部的数据结构，准备下一个CMS生命周期的使用。


**TODO: 以下两个阶段耗时>1s**
[GC (CMS Initial Mark) [1 CMS-initial-mark: 0K(2097152K)] 1426009K(3984640K), 1.0288212 secs]
[CMS-concurrent-abortable-preclean: 4.484/5.217 secs]



## 参数解析

1. 新老生代使用代垃圾收集器 ParNew and CMS
```sh
-XX:+UseConcMarkSweepGC -XX:+UseParNewGC
```
CMS的全称：Concurrent Mark and Sweep，官方给予的名称是：“Mostly Concurrent Mark and Sweep Garbage Collector”;

年轻代：采用 stop-the-world mark-copy 算法(STW)；
年老代：采用 Mostly Concurrent mark-sweep 算法；
设计目标：年老代收集的时候避免长时间的暂停；


2. Heap
```sh
-Xmx1G # 设置堆的最大值 等同于-XX:MaxHeapSize=1G; Specifies the maximum size (in bytes) of the memory allocation pool in bytes. This value must be a multiple of 1024 and greater than 2 MB.
-Xms1G # 设置堆的初始大小 Sets the initial size (in bytes) of the heap. This value must be a multiple of 1024 and greater than 1 MB
-Xmn512M # 设置新生代大小,等同于 -XX:NewSize=512M ; 官方说明：Sets the initial size of the heap for the young generation (nursery)
-XX:NewRatio=2 # 新生代与老年代的比例， 2表示 New:Old = 1：2 ，意思是 Old 占 2/3
-XX:SurvivorRatio=8 # 值为8表示： Eden : Survivor To: Survivor From = 8:1:1;   值为6表示 6:1:1
```


### GC调优的目的可以总结为下面两点：
- 减少对象晋升到老年代的数量
- 减少FullGC的执行时间
如果通过减少老年代的大小来降低Full GC的执行时间，可能会引发OutOfMemoryError或者导致Full GC的频率升高。
如果是通过增加老年代的大小来降低Full GC的频率，执行时间将会增加  
> 如何达到一个平衡？

### 优化参考数值
- Minor GC执行非常迅速（50ms以内）
- Minor GC没有频繁执行（大约10s执行一次）
- Full GC执行非常迅速（1s以内）
- Full GC没有频繁执行（大约10min执行一次）

### GC调优实例参考
- GC时间过长：<https://segmentfault.com/a/1190000005174819>
```
# 添加下面两个参数强制在remark阶段和FULL GC阶段之前先在进行一次年轻代的GC，这样需要进行处理的内存量就不会太多。
-XX:+ScavengeBeforeFullGC 
-XX:+CMSScavengeBeforeRemark
```

- [OOM](https://mp.weixin.qq.com/s?__biz=MzAwNTQ4MTQ4NQ==&mid=2453559994&idx=1&sn=4859ab4b755890515921e9d5bbeca597&scene=21#wechat_redirect)
- [降低abortable preclean 时间，而且不增加final remark的时间](https://blog.csdn.net/flysqrlboy/article/details/88679457)


## 参考
- [Jvm 系列(四):Jvm 调优-命令篇](http://www.ityouknow.com/jvm/2017/09/03/jvm-command.html)
- 官方文档： <https://docs.oracle.com/javase/8/docs/technotes/tools/unix/>
- <https://www.cnblogs.com/zhangxiaoguang/p/5792468.html>
