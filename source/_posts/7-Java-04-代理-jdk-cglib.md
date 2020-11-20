---
title: 从代理模式到JDK动态代理，CGLib
comments: true
date: 2019-10-23 20:30
categories: [Java]
tags: [Java]
---

## 静态代理
![代理模式-UML](http://www.plantuml.com/plantuml/png/SoWkIImgAStDuIh9BCb9LNYoU_7p2MtFLYnykgUVApEl9BKeBJ4vLI6uD2ahDRcacaj1GRiejR0qjRX4GzEQgvQBApadiRXO8IWrCGUgHY4pjo0dDJSrhwGOkxRgMYID0KNv5PKujEWYcrg4O5jZCmvYa9wU7R9Rk1nIyrA0dWC0)

<details>
<summary>代理模式-plantuml</summary>
@startuml
title 代理模式
interface Subject{
  +request();
}

class RealSubject implements Subject{
  +request(){};
}

class Proxy implements Subject{
  -RealSubject realSubject;
  +request(){};
}

Proxy ..> RealSubject
@enduml
</details>

## 动态代理

### jdk动态代理

JDK动态代理主要涉及java.lang.reflect包下边的两个类：Proxy和InvocationHandler。其中，InvocationHandler是一个接口，可以通过实现该接口定义横切逻辑，并通过反射机制调用目标类的代码，动态地将横切逻辑和业务逻辑编织在一起

jdk动态代理的限制是只能为接口创建代理实例。

**JDK动态代理具体实现原理：**
- 通过实现InvocationHandler接口创建自己的调用处理器；
- 通过为Proxy类指定ClassLoader对象和一组interface来创建动态代理；
- 通过反射机制获取动态代理类的构造函数，其唯一参数类型就是调用处理器接口类型；
- 通过构造函数创建动态代理类实例，构造时调用处理器对象作为参数参入

<!-- more -->
### CGLib

CGLib采用底层的字节码技术，全称是：Code Generation Library，CGLib可以为一个类创建一个子类，在子类中采用方法拦截的技术拦截所有父类方法的调用并顺势织入横切逻辑。

被final关键字所修饰的类不能实现CGLib代理。

## 效率对比

关于两者之间的性能的话，JDK动态代理所创建的代理对象，在以前的JDK版本中，性能并不是很高，虽然在高版本中JDK动态代理对象的性能得到了很大的提升，但是他也并不是适用于所有的场景。主要体现在如下的两个指标中：
- CGLib所创建的动态代理对象在实际运行时候的性能要比JDK动态代理高不少，有研究表明，大概要高10倍；
- 但是CGLib在创建对象的时候所花费的时间却比JDK动态代理要多很多，有研究表明，大概有8倍的差距；
- 因此，对于singleton的代理对象或者具有实例池的代理，因为无需频繁的创建代理对象，所以比较适合采用CGLib动态代理，反之，则比较适用JDK动态代理。

最终的测试结果大致是这样的，在1.6和1.7的时候，JDK动态代理的速度要比CGLib动态代理的速度要慢，但是并没有以上描述的10倍差距，在JDK1.8的时候，JDK动态代理的速度已经比CGLib动态代理的速度快很多了。


## 其他注意点

通过字节码可以看到cglib是直接继承了原有类，实现了Factory接口，而jdk是实现了自己的顶层接口，继承了Proxy接口。这样的话，按照类来找话，jdk就找不到他的实现了，因为他的实现类实际上是一个Proxy类，而不是他自己。

SpringBoot 中，`@EnableAspcetJAutoProxy` 和 `@EnableAsync` 两个注解均可以通过设置`proxyTargetClass=true`参数来强制使用CGLib方式代理。 该参数默认为false,即如果实现Interface则使用jdk动态代理，没有Interface才使用CGLib

## 参考
- <https://juejin.im/entry/6844903673676431374>
- [透过现象看原理：详解Spring中Bean的this调用导致AOP失效的原因](https://my.oschina.net/guangshan/blog/1807721)

