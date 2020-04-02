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

<!-- more -->
## Spring 相关
- IOC控制反转(依赖注入AI)
  - 解释: 以前对象是有程序本身来创建,使用spring后,程序变为被动接收spring创建好的对象
  - IOC（Inverse Of Control），控制反转。所谓控制反转，就是把创建对象（Bean）和维护对象（Bean）关系的权力从程序中转移到Spring容器中，而程序本身不再负责维护。
  - 



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


# 下一步计划
- dubbo -- [ok]
  - rpc实现原理, 源码阅读
- druid
- zookeeper 
  - <https://mp.weixin.qq.com/s/Myp7aqwnhGB419e0Gu10bQ>
  - 待实现
- RabbitMQ -- [ok]
- RocketMQ 
- AOP(面向切面编程) `@Aspect`
- Spring 源码阅读,IOC容器

## 偏运维
- Docker 容器（虚拟化）
- Jenkins 持续集成（开发->测试->部署）
- k8s (Kubernetes) 

- gitlab 代码库管理
- gerrit 代码code review后合并到gitlab
- sonar 代码质量检测
- nexus 私服npm,pip,Maven
- CF (confluence) 文档管理



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

