---
title: Java 学习记录-1
comments: true
date: 2019-08-12 10:57
categories: [Java]
tags: [Java]
---

# 记录java学习过程中的一些问题

## 小白问题

1. POJO 是什么?
POJO（Plain Ordinary Java Object / Pure Old Java Object）简单的Java对象，实际就是普通JavaBeans，是为了避免和EJB混淆所创造的简称。
使用POJO名称是为了避免和EJB混淆起来, 而且简称比较直接. 其中有一些属性及其getter setter方法的类,没有业务逻辑，有时可以作为 **VO(value -object)**或 **DTO(Data Transform Object)**来使用.当然,如果你有一个简单的运算属性也是可以的,但不允许有业务方法,也不能携带有connection之类的方法。

2. POJO
  - 这个类没有实现/继承任何特殊的java接口或者类

* 与JavaBean的区别
> JavaBean 是一种JAVA语言写成的可重用组件。JavaBean符合一定规范编写的Java类，不是一种技术，而是一种规范
  - 所有属性为private。
  - 这个类必须有一个公共的缺省构造函数。即是提供无参数的构造器。
  - 这个类的属性使用getter和setter来访问，其他方法遵从标准命名规范。
  - 这个类应是可序列化的。实现serializable接口。

* 两者有什么区别
  - POJO其实是比javabean更纯净的简单类或接口。POJO严格地遵守简单对象的概念，而一些JavaBean中往往会, 封装一些简单逻辑。
  - POJO主要用于数据的临时传递，它只能装载数据，作为数据存储的载体，而不具有业务逻辑处理的能力。
  - Javabean虽然数据的获取与POJO一样，但是javabean当中可以有其它的方法。


3. devtools 开发环境热部署
> (https://blog.csdn.net/qq_27886997/article/details/82799217)



4. 空指针异常（NPE : NullPointerException)

5. 常用目录名称
![java项目目录结构](/assets/images/tech/java项目目录结构.png "java项目目录结构")
```
pojo
entity
domain
dao
mapper
repository
bean

config
utils
vo

web
model
controller
service

```

- 代码层的结构1
```
根目录：com.ven

1.工程启动类(ApplicationServer.java)置于com.ven.build包下

2.实体类(domain)置于com.ven.domain

3.数据访问层(Dao)置于com.ven.repository

4.数据服务层(Service)置于com,ven.service,数据服务的实现接口(serviceImpl)至于com.ven.service.impl

5.前端控制器(Controller)置于com.ven.controller

6.工具类(utils)置于com.ven.utils

7.常量接口类(consist)置于com.ven.consist

8.配置信息类(config)置于com.ven.config

9.数据传输类(vo)置于com.ven.vo
```

- 代码结构2
> 可参考开源项目：(https://gitee.com/aun/Timo)
```
根目录：net.csdn

1.启动类（CsdnApplication.java）推荐放在根目录net.csdn包下

2.实体类（domain）

    A: net.csdn.domain（jpa项目）

    B: net.csdn.pojo（mybatis项目）

3.数据接口访问层(Dao)

    A: net.csdn.repository（jpa项目）

    B: net.csdn.mapper（mybatis项目）

4.数据服务接口层（Service）推荐：net.csdn.service

5.数据服务实现层（Service Implements）推荐：net.csdn.service.impl

    ——使用idea的同学推荐使用net.csdn.serviceImpl目录

6.前端控制器层（Controller）推荐：net.csdn.controller

7.工具类库（utils）推荐：net.csdn.utils

8.配置类（config）推荐：net.csdn.config

9.数据传输对象（dto）推荐：net.csdn.dto

    ——数据传输对象（Data Transfer Object）用于封装多个实体类（domain）之间的关系，不破坏原有的实体类结构

10.视图包装对象（vo）推荐：net.csdn.vo

    ——视图包装对象（View Object）用于封装客户端请求的数据，防止部分数据泄露（如：管理员ID），保证数据安全，不破坏   原有的实体类结构

```

- 其他理解
```
#--------------
Entity接近原始数据，Model接近业务对象～ 
Entity：是专用于EF的对数据库表的操作， 
Model：是为页面提供数据和数据校验的，所以两者可以并存 


#-------------
model是一个数据模型，里面装的是数据，也就是你视图前端要用的东西，显示哪些字段就可以用model 直接返回视图。基于视图显示字段决定，方便你自己用。
而entity是实体模型，对应的是表和表结构，和数据库相关的，基于数据库中的表了。


```

6. 一些常用注解的理解
> [【SpringBoot系列】SpringBoot注解详解](https://zhuanlan.zhihu.com/p/50716249)
```java

@Data

@Entity

@Bean

@Component

@Configuration

@SpringBootApplication
/**
@SpringBootApplication 相当于下面三个注解
@EnableAutoConfiguration
@ComponentScan
@Configuration
*/

@RestController
/**
RestController注解是@Controller和@ResponseBody的合集,表示这是个控制器bean,并且是将函数的返回值直 接填入HTTP响应体中,是REST风格的控制器。
*/

```

7. Spring Cloud的微服务应用 ?


8. java技术栈
![java职业路线](/assets/images/tech/java职业路线.jpg "java职业路线")

## 一些配置

- application.properties
```
# db
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.url=jdbc:mysql://localhost:3306/springdb?serverTimezone=GMT

# JPA 相关配置
# 自动创建hibernate_sequence表
#spring.jpa.hibernate.ddl-auto=create
spring.jpa.show-sql=true

# 开发环境热部署
spring.devtools.restart.enabled=true
spring.devtools.restart.additional-paths=src/**
```

- pom.xml
```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.17</version>
    </dependency>

```


## Java Php Nodejs 的一些对比




## 参考的网址
> [SpringBoot入门，快速搭建简单Web应用环境](https://blog.csdn.net/qq_27317475/article/details/81119098)  
> [SpringBoot入门，整合Mybatis并使用Mybatis-Generator自动生成所需代码](https://blog.csdn.net/qq_27317475/article/details/81143651)
> [十五分钟用Spring Boot+MySQL做一个登陆系统](https://zhuanlan.zhihu.com/p/55796334)
