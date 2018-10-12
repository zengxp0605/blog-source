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
> Functions that use pointers or references to base classes must be able to use objects of dervied classes without knowing it.
> 所有引入基类的地方必须能透明地使用其子类对象

- 此原则针对`继承`, 包含了4层含义
  - 子类必须完全实现父类的方法
  - 子类可以用于自己的个性
  - 覆盖或实现父类的方法时,输入参数可以被放大
  - 覆盖或实现父类的方法时,输出结果可以被缩小


### 依赖倒置原则

### 接口隔离原则

### 开闭原则


<!--more-->

