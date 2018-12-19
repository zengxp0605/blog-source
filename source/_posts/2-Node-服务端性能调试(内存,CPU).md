---
title: nodejs内存监控,及内存泄漏调试
comments: true
date: 2018-12-11 19:25
categories: [nodejs]
tags: [nodejs]
keywords: 
---


## 内存监控工具
1. Memeye 是一个轻量级的 NodeJS 进程监控工具 
<https://github.com/JerryC8080/Memeye>

- 使用简单,可视化

2. Easy-Monitor 2.0
<https://github.com/hyj1991/easy-monitor>  

- windows下无法正常使用

3. 内存快照
<https://github.com/bnoordhuis/node-heapdump>

- 生成不同时间点的快照, 通过Chrome的DevTools对比


### Chrome DevTools 内存分析
<http://yuanfentiank789.github.io/2016/06/29/Shallow_Retained/>
[JavaScript 内存分析](http://wiki.jikexueyuan.com/project/chrome-devtools/javascript-memory-profiling.html)
- Shallow size
Shallow size就是对象本身占用内存的大小，不包含其引用的对象。

- Retained size
Retained size是该对象自己的shallow size，加上从该对象能直接或间接访问到对象的shallow size之和。换句话说，retained size是该对象被GC之后所能回收到内存的总和。

- Distance
Distance 指的是从根节点到当前节点的距离


## nodejs运行时效率分析

### node-clinic模块: <https://github.com/nearform/node-clinic>
- 通过`clinic flame -- node server.js` 执行对应的脚本,可以生成火焰图,查看执行效率低的函数



## 说明
以上方法都是分析的`堆内存`, 对于`Nodejs`执行时的`Rss`内存则没有好方法检测到底是哪里的问题?

- 以下是有可能导致rss占用居高不下,而堆内存稳定的情况
  - 网络I/O密集
  - Buffer 使用过多

- 例如`Primus.js`推送消息集中时,可能导致cpu飙升,同时rss内存持续上涨,及时任务完成也不会下降到初始值


## 其他参考文章

[实用小指南——教你如何在 Node.js 中发现 JavaScript 内存泄露](https://www.oschina.net/translate/simple-guide-to-finding-a-javascript-memory-leak-in-node-js?print)