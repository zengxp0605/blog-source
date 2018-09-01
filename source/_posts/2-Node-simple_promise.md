---
title: Nodejs promise的简单实现
comments: true
date: 2016-4-17 09:23
categories: [nodejs]
tags: [nodejs,promise]
keywords: nodejs promise
---

### Promises/A 规范
promise 表示一个最终值，该值由一个操作完成时返回。

promise 有三种状态：未完成 (unfulfilled)，完成 (fulfilled) 和失败 (failed)。
promise 的状态只能由未完成转换成完成，或者未完成转换成失败 。
promise 的状态转换只发生一次。

### 捕获Promise中未catch的异常
- 异常:
```
 UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): ReferenceError: common.log is not defined
```

- 通过进程捕获,并得到错误堆栈
```js
process.on('unhandledRejection', (reason, p) => {
  console.log('Unhandled Rejection at:', p, 'reason:', reason);
  // application specific logging, throwing an error, or other logic here
});
```


#### 参考:
> [https://cnodejs.org/topic/569c8226adf526da2aeb23fd](https://cnodejs.org/topic/569c8226adf526da2aeb23fd)  


