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

<!-- more -->

### 里氏替换原则
> LSP: Liskov Substitution Principle
> Functions that use pointers or references to base classes must be able to use objects of dervied classes without knowing it.
> 所有引入基类的地方必须能透明地使用其子类对象

- 此原则针对`继承`, 包含了4层含义
  - 子类必须完全实现父类的方法
  - 子类可以用于自己的个性
  - 覆盖或实现父类的方法时,输入参数可以被放大
  - 覆盖或实现父类的方法时,输出结果可以被缩小


### 依赖倒置原则
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

网上的一些理解：
1. 把原本的高层建筑依赖底层建筑倒置过来，变成底层建筑依赖高层建筑。 高层建筑需要什么，底层去实现这样的需求，但是高层并不管底层是怎么是怎么实现的。
如原来，汽车依赖车身，车身依赖底盘，底盘依赖轮胎  
倒置之后，轮胎依赖底盘，底盘依赖车身，车身依赖汽车
2. 依赖倒置原则的具体的实现，可以体现在spring的IOC(Inversion of Control 控制反转)，使用依赖注入(DI)来实现


### 接口隔离原则
> ISP: Interface Segregation Principle
> Clients should not be forced to depend upon interfaces that they don't use.(客户端不应该依赖它不需要的接口)
> The dependency of one class to another one should depend on the smallest possible interface.(类间的依赖关系应该建立在最小的接口上)

- 保证接口的纯洁性
  - 接口要尽量小(首先必须要满足单一职责原则,已经是单一职责的接口则不再拆分)
  - 接口要高内聚: 高内聚就是提高接口/类/模块的处理能力,减少对外的交互
  - 定制服务
  - 接口设计是有限度的


### 迪米特法则（最少知识原则）
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



<!--more-->

## 其他设计原则
- 组合 / 聚合复用原则（Composition/Aggregation Reuse Principle - CARP）
当要扩展类的功能时，优先考虑使用组合，而不是继承。这条原则在 23 种经典设计模式中频繁使用，如：代理模式、装饰模式、适配器模式等

- 无环依赖原则（Acyclic Dependencies Principle - ADP）
当 A 模块依赖于 B 模块，B 模块依赖于 C 模块，C 依赖于 A 模块，此时将出现循环依赖。在设计中应该避免这个问题，可通过引入 “中介者模式” 解决该问题； 
中心化，网状结构-->星型结构

- 保持它简单与傻瓜（Keep it simple and stupid - KISS）
不要让系统变得复杂，界面简洁，功能实用，操作方便，要让它足够的简单，足够的傻瓜。

- 高内聚与低耦合（High Cohesion and Low Coupling - HCLC）
模块内部需要做到内聚度高，模块之间需要做到耦合度低。



# 设计模式

分类  
创建型：单例，工厂，建造者，原型  
行为性：策略，模版，中介者，观察者，责任链，命令，解释器，迭代器，备忘录，状态，空对象，访问者  
结构型：代理，外观，组合，装饰器，适配器，桥接，过滤器，享元  


## 单例模式
详见: <https://github.com/zengxp0605/java-design-patterns/tree/master/02-singleton>

Spring中，通过@Autowired注入的依赖，默认是单例模式的
写法和使用场景参考: [设计模式学习笔记（8）单例](https://b.letec.top/106.html)

## 工厂模式
详见: <https://github.com/zengxp0605/java-design-patterns/tree/master/03-factory>

- 简单工厂模式
提供一个统一的工厂类，根据传入的参数不同返回不同的实例，被创建的实例具有共同的父类或接口
适用于需要创建的对象较少

- 工厂方法模式
工厂方法模式是简单工厂的仅一步深化， 在工厂方法模式中，我们不再提供一个统一的工厂类来创建所有的对象，而是针对不同的对象提供不同的工厂。也就是说每个对象都有一个与之对应的工厂

- 抽象工厂模式
看起来代码变多来，实际是解藕，代码方便维护
结合策略模式修改 AbstractFactory 类，代码更优雅


## 模板方法模式
详见: <https://github.com/zengxp0605/ts-design-patterns/tree/master/src/4_template_method>

## 建造者模式
详见: <https://github.com/zengxp0605/ts-design-patterns/tree/master/src/5_builder>

## 代理模式
详见:  
- <https://github.com/zengxp0605/ts-design-patterns/tree/master/src/6_proxy>
- <https://github.com/zengxp0605/java-design-patterns/tree/master/01-proxy>

代理模式小结
- 代理模式角色分为 3 种：
    - Subject（抽象主题角色）：定义代理类和真实主题的公共对外方法，也是代理类代理真实主题的方法；
    - RealSubject（真实主题角色）：真正实现业务逻辑的类；
    - Proxy（代理主题角色）：用来代理和封装真实主题；

- 特点
    - 两个对象，被代理人和代理人
    - 被代理人的事情必须要做，但是自己不想做，或者做得不专业，需要代理人来做
    - 代理人可以拿到被代理人的个人资料 

- 关注过程，不关心结果 
- 动态代理底层： **字节码重组**       


## 原型模式
> Prototpe Pattern  
> Specify the kinds fo objects to create using a prototypical instance, and create new objects by copying this prototype. (用原型实例指定创建对象的种类,并且通过拷贝这些原型创建新的对象.)
- Java中通过实现 `Cloneable` 接口来实现

## 中介者模式

## 命令模式

## 责任链模式

## 装饰模式

## 策略模式
> Define a family of algorithms, encapsulate each one, and make them interchangeable. [The] Strategy [pattern] lets the algorithm vary independently from clients that use it.
详见:  
- <https://github.com/zengxp0605/java-design-patterns/tree/master/04-strategy-base>
- springboot注解实现策略模式 @Autowired： <https://github.com/zengxp0605/spring-boot-mydemo/tree/master/13-springboot-strategy>

角色：
- Strategy: 策略接口或者策略抽象类，并且策略执行的接口
- ConcreteStrategyA，ConcreteStrategyB等：实现策略接口的具体策略类
- Context：上下文类，持有具体策略类的实例，并负责调用相关的算法

特点：
- 一组算法
- 客户端自由切换

优点：
- 策略模式提供了对“开闭原则”的完美支持，用户可以在不修改原有系统的基础上选择算法（策略），并且可以灵活地增加新的算法（策略）

缺点：
- 客户端必须知道所有的策略类，并自行决定使用哪一个策略类


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


---------
# 23种之外的设计模式

## 委派模式
![委派模式类图](/assets/images/tech/委派模式类图.png "委派模式类图")
特征：
- 相当于静态代理一种非常特殊的情况，全权代理
- 项目经理分配任务，需要选择一个员工去执行，像策略模式
- 干活是我的，功劳是你的
- spring中以 Delegate, Dispatcher开头/结尾的类，一般是委托模式，如`DispatcherServlet`

**代理模式和策略模式的综合体？？**



-----------
# 疑惑？？？

使用各种设计模式，感觉很多都有关联性

比如：
策略模式中的Context 似乎也符合组合模式？

委派模式也可理解为策略模式？ 委派给不同的对象执行，换个说法也就是使用不同的策略去执行；
如项目经理委派任务给员工A或者员工B， 那么也可以理解为项目经理使用了“员工A”策略，或者使用了“员工B”策略

委派模式跟代理模式（静态代理）也很相似？ 假如代理模式中的代理在 invoke 前后不做操作，那么跟委托模式描述是一样的？


> 委托模式是一项基本技巧，许多其他的模式，如状态模式、策略模式、访问者模式本质上是在更特殊的场合采用了委托模式
