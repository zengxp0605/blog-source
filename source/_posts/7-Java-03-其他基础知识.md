---
title: Java 学习记录-3
comments: true
date: 2019-11-02 10:57
categories: [Java]
tags: [Java]
---


## web容器的简介

- Tomcat
> 官网: The Apache Tomcat® software is an open source implementation of the Java Servlet, JavaServer Pages, Java Expression Language and Java WebSocket technologies. 

- Jetty
> 官网介绍: Eclipse Jetty provides a Web server and javax.servlet container, plus support for HTTP/2, WebSocket, OSGi, JMX, JNDI, JAAS and many other integrations [n.集成；综合]. These components are open source and available for commercial use and distribution.  
> 维基百科: Jetty是一个纯粹的基于Java的网页服务器和Java Servlet容器。  
> 百度百科: Jetty 是一个开源的servlet容器，它为基于Java的web容器，例如JSP和servlet提供运行环境.  

- Jboss
> 基于J2EE的应用服务器  
> 开放源代码  
> JBoss核心服务不包括支持servlet/JSP的WEB容器，一般与Tomcat绑定使用，JBoss的Web容器使用的是Tomcat。

### 补充:
- Netty和Tomcat有什么区别？
> 官网: Netty is an asynchronous event-driven network application framework for rapid[adj.迅速的，急促的；飞快的；险峻的] development of maintainable high performance protocol servers & clients.
> Netty三大优点：并发高，传输快，封装好
Netty和Tomcat最大的区别就在于通信协议，Tomcat是基于Http协议的，他的实质是一个基于http协议的web容器，但是Netty不一样，他能通过编程自定义各种协议，因为netty能够通过codec自己来编码/解码字节流，完成类似redis访问的功能，这就是netty和tomcat最大的不同。 


## Spring 相关
- IOC控制反转(依赖注入AI)
  - 解释: 以前对象是有程序本身来创建,使用spring后,程序变为被动接收spring创建好的对象
  - IOC（Inverse Of Control），控制反转。所谓控制反转，就是把创建对象（Bean）和维护对象（Bean）关系的权力从程序中转移到Spring容器中，而程序本身不再负责维护。
  - 


## mybatis 相关
> 半自动的ORM框架
- 获取insert 返回的自增长id

注解的方式,在接口映射器中设置useGeneratedKeys参数
```java
@Options(useGeneratedKeys = true, keyProperty = "id", keyColumn = "id")
@Insert("insert into test(name,descr,url,create_time,update_time) values(#{name},#{descr},#{url},now(),now())")
```

xml配置,在xml映射器中配置useGeneratedKeys参数
```xml
<insert id="insertOneTest" parameterType="com.jason.mybatisxml.model.User" useGeneratedKeys="true" keyProperty="id" keyColumn="id">
    INSERT INTO
        users
        (userName,passWord,user_sex,nick_name)
    VALUES
        (#{userName}, #{passWord}, #{userSex}, #{nickName})
</insert>
```

## JVM 相关
- [推荐收藏系列：一文理解JVM虚拟机（内存、垃圾回收、性能优化）解决面试中遇到问题](https://juejin.im/post/5d200b54f265da1bac40384a)

- JVM跨平台实现
> 一次编译，到处执行
  - 通过`javac.exe`编译器编译`.java`文件得到的`.class`文件是不能直接运行的，这些文件需要交给JVM来解析运行
  - JVM是运行在操作系统之上的，每个操作系统的指令是不同的，而JDK是区分操作系统的，安装的JDK是和当前系统兼容的
  - 而class字节码运行在JVM之上，所以不用关心class字节码是在哪个操作系统编译的，只要符合JVM规范，那么，这个字节码文件就是可运行的

- `class`文件和JVM
  - Java类的加载是动态的，它并不会一次性将所有类全部加载后再运行，而是保证程序运行的基础类(像是基类)完全加载到jvm中，至于其他类，则在需要的时候才加载。这当然就是为了节省内存开销
  - class文件是通过类的加载器装载到jvm中的
  - Java默认的类加载器： 
    - Bootstrap ClassLoader：负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类
    - Extension ClassLoader：负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中jre/lib/*.jar或-Djava.ext.dirs指定目录下的jar包
    - App ClassLoader：负责加载classpath中指定的jar包及目录中class
  - 双亲委派模型
    - 简单来说：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上
    - 好处： 防止内存中出现多份同样的字节码(安全性角度)
    - 类加载器在成功加载某个类之后，会把得到的 java.lang.Class类的实例缓存起来。下次再请求加载该类的时候，类加载器会直接使用缓存的类的实例，而不会尝试再次加载
  - 类加载的详细过程
    - 加载，查找并加载类的二进制数据，在Java堆中也创建一个java.lang.Class类的对象
    - 连接，连接又包含三块内容：验证、准备、解析
    - 初始化，为类的静态变量赋予正确的初始值
    - 使用，new出对象程序中使用
    - 卸载，执行垃圾回收  


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


## java多线程
- Thread.yield()方法
```
# jdk注释
A hint to the scheduler that the current thread is willing to yield its current use of a processor. The scheduler is free to ignore this hint.

翻译: 向调度程序提示当前线程愿意放弃当前使用的处理器。 调度程序可以随意忽略此提示
```

- yield 和 sleep 的异同
  1）yield, sleep 都能暂停当前线程，sleep 可以指定具体休眠的时间，而 yield 则依赖 CPU 的时间片划分。
  2）yield, sleep 两个在暂停过程中，如已经持有锁，则都不会释放锁资源。
  3）yield 不能被中断，而 sleep 则可以接受中断。

- yield 方法可以很好的控制多线程，如执行某项复杂的任务时，如果担心占用资源过多，可以在完成某个重要的工作后使用 yield 方法让掉当前 CPU 的调度权，等下次获取到再继续执行，这样不但能完成自己的重要工作，也能给其他线程一些运行的机会，避免一个线程长时间占有 CPU 资源。


## 序列化 `Serilizable` `Externalizable`
java 的`transient`关键字为我们提供了便利，你只需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。


## Reflect
自java8开始，可以直接通过反射得到方法的参数名。取代了之前如arg0、arg1 等无含义的参数名称。不过这样有个条件：你必须手动在编译时开启-parameters 参数，否则还是获取不到。
```java
 Method method = clazz.getMethod("method1", String.class, String.class);
  //得到该方法参数信息数组
  Parameter[] parameters = method.getParameters();
  //遍历参数数组，依次输出参数名和参数类型
  Arrays.stream(parameters).forEach(p->{
      System.out.println(p.getName()+" : "+p.getType());
  });
```


# 下一步计划
- dubbo -- [ok]
  - rpc实现原理, 源码阅读
- druid
- zookeeper 
  - <https://mp.weixin.qq.com/s/Myp7aqwnhGB419e0Gu10bQ>
  - 待实现
- RabbitMQ -- [ok]
- RocketMQ 
- Docker
- AOP(面向切面编程) `@Aspect`
- Spring 源码阅读,IOC容器

- [Gatling简单测试SpringBoot工程](https://www.cnblogs.com/sanshengshui/p/9750478.html)
    - <https://www.cnblogs.com/sanshengshui/p/9747069.html>


# 读源码
- dubbo: 
- mybatis: [面试官：Mybatis 使用了哪些设计模式？](https://mp.weixin.qq.com/s?__biz=MzI3NzE0NjcwMg==&mid=2650123749&idx=1&sn=e431a3ee65843d85ac8799c2b31ac5e8&chksm=f36bb0c4c41c39d212c5758f03a605f92c0b109c1892a7ca4b8cac39261673bf45e9cd345a7b&scene=21#wechat_redirect) 


# 工具汇总
- [Hutool是一个小而全的Java工具类库](https://github.com/looly/hutool) 
- [ELK(elasticsearch+logstash+kibana) 实现 Java 分布式系统日志分析架构](https://juejin.im/entry/57e494230e3dd9005808ff9e)


# 待阅读
- [基于Netty自己动手实现RPC框架](https://zhuanlan.zhihu.com/p/35720383)
- [基于Netty自己动手实现Web框架](https://zhuanlan.zhihu.com/p/36064672)
  - [httpkids](https://github.com/pyloque/httpkids)

- 待学习的项目
  - [基于springboot+dubbo+mybatis的分布式敏捷开发框架](https://github.com/G-little/priest)

