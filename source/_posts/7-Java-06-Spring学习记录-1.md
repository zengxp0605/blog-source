---
title: Spring学习记录(1)
comments: true
date: 2019-09-23 20:30
categories: [Java]
tags: [Spring]
---


## Spring架构
spring4.x架构图
![spring4.x架构图](/assets/images/tech/spring4.x架构图.png "spring4.x架构图")


## Spring中的基本思想

### AOP
- 面向切面编程 Aspect Oriented Programing
- 找出多个类中有一定规律的代码，开发时拆开，运行时再合并
- 面向规则编程？

**解藕，专人做专事**

在Spring中的使用： 
Spring 事务管理、日志和其他各种特性的上下文中


### OOP
- 面向对象编程 Object Oriented Programing
- 类比生活中的“一切皆对象”

**封装、继承、多态**

### BOP
- 面向Bean编程 Bean Oriented Programing
- 面向Bean设计程序

**一切从Bean开始**

### IOC
- 控制反转 Inversion of Control
- 将new对象的动作交给Spring 统一管理，并由Spring将创建好的对象保存在IOC容器中

**转交控制权（控制权反转）**

### DI/DL
- 依赖注入 Dependency Injection / 依赖查找 Dependency Lookup
- Spring不仅保存创建的对象，并且保存对象与对象之间的依赖关系
- 注入即赋值，主要三种方式：构造方法、setter、直接赋值

**理清关系再赋值**

