---
title: 浅读设计模式之禅
comments: true
date: 2018-10-8 18:50
categories: [读书笔记]
tags: [读书笔记]
keywords: 设计模式之禅
---

## 六大设计原则

### 单一职责原则
> SRP: Single Responsibility Principle 
> There should never be more than one reason for a class to change

- 按照单一职责原则设计的`电话类`,实现两个接口
```
public class Phone implement IConnectionManmager, IDataTransfer {

}
```


- 通常会使用的方式, 一个`电话接口`
```
public interface IPhone {
    // 拨通电话
    public void dial(String phoneNumber);
    // 通话
    public void chat(Object o);
    // 挂断
    public void hangup();
}
```

**单一职责原则最难划分的就是职责**
一个职责一个接口,但是`职责`没有一个量化的标准,一个类到底要负责哪些职责? 这些职责怎么细化? 细化后是否都要有一个接口或类?
这些都需要从实际的项目去考虑,从功能上来说,定义一个`IPhone`接口也没有错,实现了电话的功能,而且设计还很简单,仅仅一个接口一个实现类.


- 单一职责适用于接口/类, 同样适用于方法---一个方法尽可能只做一件事情,比如一个方法`修改用户密码`,不要把这个方法放到`修改用户信息`方法中,这个方法的粒度很粗.

- 接口一定要符合单一职责原则, 而类往往是违背的, **对于单一职责原则,我的建议是接口一定要做到单一职责, 类的设计尽量做到只有一个原因引起变化**


### 里氏替换原则
<<<<<<< HEAD
> LSP: Liskov Substitution Principle
=======
>>>>>>> 562a86f7f8cd20ecd34f89dd10562d535e8eefee
> Functions that use pointers or references to base classes must be able to use objects of dervied classes without knowing it.
> 所有引入基类的地方必须能透明地使用其子类对象

- 此原则针对`继承`, 包含了4层含义
  - 子类必须完全实现父类的方法
  - 子类可以用于自己的个性
  - 覆盖或实现父类的方法时,输入参数可以被放大
  - 覆盖或实现父类的方法时,输出结果可以被缩小


### 依赖倒置原则
<<<<<<< HEAD
> DIP: Dependence Inversion Principle
> High level modules should not depend upon low level modules. Both should depend upon abstractions. Abstractions should not depend upono details. Details should depend upon abstractions.

- Java 中的理解为:
  - 模块间的依赖同构抽象发生, 实现类之间不发生直接的依赖关系, 其依赖关系是通过接口或者抽象类产生的.
  - 接口或者抽象类不依赖于实现类
  - 实现类依赖接口或者抽象类
- 更加精简的定义就是"面向接口编程" -- OOD (Object-Oriented Design, 面向对象设计)的精髓之一
  
- 两个类之间有依赖关系,只要制定出两者之间的接口(或者抽象类)就可以独立开发了,而且项目之间的单元测试也可以独立地运行,而TDD(Test-Driven Development, 测试驱动开发)开发模式就是依赖倒置原则的最高级应用

- 依赖的三种写法(依赖注入的方式)
  - 构造函数传递依赖对象(构造函数注入)
  - Setter方法传递依赖对象
  - 接口注入

- 依赖倒置原则是6个设计原则中最难以实现的原则, 它是实现开闭原则的重要途径,依赖倒置没有实现,就别想实现对扩展开发,对修改关闭. 在项目中只要记住"面向接口编程"就基本上抓住了依赖倒置原则的核心.

### 接口隔离原则
> ISP: Interface Segregation Principle
> Clients should not be forced to depend upon interfaces that they don't use.(客户端不应该依赖它不需要的接口)
> The dependency of one class to another one should depend on the smallest possible interface.(类间的依赖关系应该建立在最小的接口上)

- 保证接口的纯洁性
  - 接口要尽量小(首先必须要满足单一职责原则,已经是单一职责的接口则不再拆分)
  - 接口要高内聚: 高内聚就是提高接口/类/模块的处理能力,减少对外的交互
  - 定制服务
  - 接口设计是有限度的


### 迪米特法则
> LoD: Law of Demeter
> LKP: Least Knowledge Principle
> 一个对象应该对其他对象有最少的了解

- 只和朋友交流(Only talk to your immedate friends)
- 朋友间也是有距离的(不要暴露太多的public方法)
- 是自己的就是自己的
  - 如果一个方法放在本类中,既不增加类间的关系,也不对本类产生负面的影响,就放置在本类中
- 谨慎使用Serializable -- **不懂这个**
  - 补充:Java的对象序列化是指将那些实现了Serializable接口的对象转换成一个字符序列，并能够在以后将这个字节序列完全恢复为原来的对象

- 迪米特法则的核心观念就是类间解耦,弱耦合才能提高类的复用率
  - 其要求的结果就是产生了大量的中转或者跳转类,导致系统的复杂度提高
  - 采用此法则时需要反复权衡,既要做到结构清晰,又要做到高内聚低耦合

### 开闭原则
> OCP: Open Closed Principle
> Software entities like classes,modules and functions should be open for extension but cloased for modifications.(一个软件实体如类/模块和函数,应该对扩展开放,对修改关闭)

- 开闭原则是最基础的一个原则,前5个原则都是开闭原则的具体形态,也就是说前5个原则就是知道设计的工具和方法,而开闭原则才是其精神领袖

=======

### 接口隔离原则

### 开闭原则
>>>>>>> 562a86f7f8cd20ecd34f89dd10562d535e8eefee


<!--more-->

<<<<<<< HEAD
# 设计模式

## 单例模式

## 工厂方法模式

## 抽象工厂模式

## 模板方法模式

## 建造者模式

## 代理模式

## 原型模式

## 中介者模式

## 命令模式

## 责任链模式

## 装饰模式

## 策略模式

## 适配器模式

## 迭代器模式

## 组合模式

## 观察者模式

## 门面模式

## 备忘录模式

## 访问者模式

## 状态模式

## 解释器模式

## 享元模式

## 桥梁模式
=======
>>>>>>> 562a86f7f8cd20ecd34f89dd10562d535e8eefee
