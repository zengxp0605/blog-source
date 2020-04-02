---
title: JVM简单理解-1
comments: true
date: 2020-03-24 10:57
categories: [Java]
tags: [Java]
---


## JVM 相关
- [推荐收藏系列：一文理解JVM虚拟机（内存、垃圾回收、性能优化）解决面试中遇到问题](https://juejin.im/post/5d200b54f265da1bac40384a)

## JVM跨平台实现
> 一次编译，到处执行
  - 通过`javac.exe`编译器编译`.java`文件得到的`.class`文件是不能直接运行的，这些文件需要交给JVM来解析运行
  - JVM是运行在操作系统之上的，每个操作系统的指令是不同的，而JDK是区分操作系统的，安装的JDK是和当前系统兼容的
  - 而class字节码运行在JVM之上，所以不用关心class字节码是在哪个操作系统编译的，只要符合JVM规范，那么，这个字节码文件就是可运行的

## 类的加载器 `.class`文件

### Java类的加载是动态的，它并不会一次性将所有类全部加载后再运行，而是保证程序运行的基础类(像是基类)完全加载到jvm中，至于其他类，则在需要的时候才加载。这当然就是为了节省内存开销

### class文件是通过类的加载器装载到jvm中的
  - 类的加载指的是将类的.class文件中的二进制数据读入到内存中，将其放在运行时数据区的方法区内，然后在堆区创建一个`java.lang.Class`对象，用来封装类在方法区内的数据结构。类的加载的最终产品是位于堆区中的Class对象，Class对象封装了类在方法区内的数据结构，并且向Java程序员提供了访问方法区内的数据结构的接口
### Java默认的类加载器： 
  - Bootstrap ClassLoader：负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类
  - Extension ClassLoader：负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中jre/lib/*.jar或-Djava.ext.dirs指定目录下的jar包
  - App ClassLoader：负责加载classpath中指定的jar包及目录中class
### 双亲委派模型
  - 简单来说：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上
  - 好处： 
    - 防止内存中出现多份同样的字节码(安全性角度)
    - 保证Java程序安全稳定运行
  - 类加载器在成功加载某个类之后，会把得到的 java.lang.Class类的实例缓存起来。下次再请求加载该类的时候，类加载器会直接使用缓存的类的实例，而不会尝试再次加载

### 类加载的详细过程(类的生命周期)
  - 加载(Loading): 查找并加载类的二进制数据，在Java堆中也创建一个java.lang.Class类的对象
  - 连接: 连接又包含三块内容：验证(Verification)、准备(Preparation)、解析(Resolution)
  - 初始化(Initialization)，为类的静态变量赋予正确的初始值
  - 使用(Using)，new出对象程序中使用
  - 卸载(Unloading)，执行垃圾回收  
在这五个阶段中，加载、验证、准备和初始化这四个阶段发生的顺序是确定的，而解析阶段则不一定，它在某些情况下可以在初始化阶段之后开始，这是为了支持Java语言的运行时绑定（也成为动态绑定或晚期绑定）。
另外注意这里的几个阶段是按顺序开始，而不是按顺序进行或完成，因为这些阶段通常都是互相交叉地混合进行的，通常在一个阶段执行的过程中调用或激活另一个阶段。


### JIT即时编辑器
- 对Java字节码重新编译优化，生成机器码，让cpu执行
- 一般对**热点代码**做编译


## JMM (Java Memory Model) java内存模型
- Java内存模型的承诺
  - 原子性
    - 原子性指的是一个操作是不可中断的，即使是在多线程环境下，一个操作一旦开始就不会被其他线程影响
    - JVM自身提供的对基本数据类型读写操作的原子性外，对于方法级别或者代码块级别的原子性操作，可以使用synchronized关键字或者重入锁(ReentrantLock)保证程序执行的原子性
  - 可见性
    - 可见性指的是当一个线程修改了某个共享变量的值，其他线程是否能够马上得知这个修改的值
  - 有序性
    - 对于指令重排导致的可见性问题和有序性问题，则可以利用volatile关键字解决，因为volatile的另外一个作用就是禁止重排序优化
- volatile关键字有如下两个作用
  - 保证被volatile修饰的共享变量对所有线程总数可见的，也就是当一个线程修改了一个被volatile修饰共享变量的值，新值总数可以被其他线程立即得知。
  - 禁止指令重排序优化。
  - 并不能保证原子性

- 参考: [Java并发编程：volatile关键字解析](https://www.cnblogs.com/dolphin0520/p/3920373.html)

## windows/mac 下调试java性能分析
>  jvisualvm可视化工具的使用(需要使用管理员权限运行 jdk/bin/jvisualvm.exe) <https://www.cnblogs.com/baihuitestsoftware/articles/6477680.html>

- `jvisualvm` GC插件查看新老生代内存情况
- 使用`jvisualvm`工具获取到java进程pid
- `jstack pid`


## 拿些常见的JVM面试题来做做，加深一下理解和查缺补漏：

1、详细jvm内存模型
2、讲讲什么情况下回出现内存溢出，内存泄漏？
3、说说Java线程栈
4、JVM 年轻代到年老代的晋升过程的判断条件是什么呢？
5、JVM 出现 fullGC 很频繁，怎么去线上排查问题？
6、类加载为什么要使用双亲委派模式，有没有什么场景是打破了这个模式？
7、类的实例化顺序
8、JVM垃圾回收机制，何时触发MinorGC等操作
9、JVM 中一次完整的 GC 流程（从 ygc 到 fgc）是怎样的
10、各种回收器，各自优缺点，重点CMS、G1
11、各种回收算法
12、OOM错误，stackoverflow错误，permgen space错误


## 参考
- [java类的加载机制](https://www.cnblogs.com/ityouknow/p/5603287.html)
- [https://www.cnblogs.com/ityouknow/p/5603287.html](https://juejin.im/post/5b45ef49f265da0f5140489c)