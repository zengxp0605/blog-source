---
title: Java 学习记录-2
comments: true
date: 2019-08-24 10:57
categories: [Java]
tags: [Java]
---

## 集合 collection
![java集合框架](/assets/images/tech/java集合框架.png "java集合框架")
![java-coll](/assets/images/tech/java-coll.png "java-coll")

从上面的集合框架图可以看到，Java 集合框架主要包括两种类型的容器，一种是集合（Collection），存储一个元素集合，另一种是图（Map），存储键/值对映射。Collection 接口又有 3 种子类型，List、Set 和 Queue，再下面是一些抽象类，最后是具体实现类，常用的有 ArrayList、LinkedList、HashSet、LinkedHashSet、HashMap、LinkedHashMap 等等。


### 集合类
- LinkedList
该类实现了List接口，允许有null（空）元素。主要用于创建链表数据结构，该类没有同步方法，如果多个线程同时访问一个List，则必须自己实现访问同步，解决方法就是在创建List时候构造一个同步的List。例如：
```java
Listlist=Collections.synchronizedList(newLinkedList(...));
```
*LinkedList 查找效率低。*

- ArrayList
该类也是实现了List的接口，实现了可变大小的数组，随机访问和遍历元素时，提供更好的性能。该类也是非同步的,在多线程的情况下不要使用。ArrayList 增长当前长度的50%，插入删除效率低。

- HashSet
该类实现了Set接口，不允许出现重复元素，不保证集合中元素的顺序，允许包含值为null的元素，但最多只能一个。

- HashMap 
HashMap 是一个散列表，它存储的内容是键值对(key-value)映射。
该类实现了Map接口，根据键的HashCode值存储数据，具有很快的访问速度，最多允许一条记录的键为null，不支持线程同步。

- LinkedHashMap 
继承于HashMap，使用元素的自然顺序对元素进行排序.



## 参考
- [java集合、Iterator迭代器、增强for循环 、泛型实例讲解](http://www.manongzj.com/blog/1-eziafihphrppsew.html)
