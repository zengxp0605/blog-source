---
title: Java 学习记录-3
comments: true
date: 2019-09-02 10:57
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









