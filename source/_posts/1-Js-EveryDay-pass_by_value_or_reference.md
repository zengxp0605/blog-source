---
title: 每天学点js-传值机制
comments: true
date: 2016-04-13 20:00
categories: [js]
tags: [每天学点js,js]
keywords: js传值
---

### 直接赋值时(非基本类型数据)

```javascript
    var me = {name: 'Tom'}; 

    var self = me;

    self.name = 'Change a name';
    console.log(self);  // output: Object {partOf: "Change a name"}
    console.log(me);    // output: Object {partOf: "Change a name"}

    // 改变self 
    self = {name : 'Jason'};

    console.log(self);  // output: {name: "Jason"}
    console.log(me);    // output: {partOf: "Change a name"}
```


### 函数传递时
```javascript
    var a = 1;
    var obj = {
        b: 2
    };
    var fn = function () {};
    fn.c = 3;
     
    function test(x, y, z) {
        x = 4;
        y.b = 5;
        z.c = 6;
        return z;
    }
    test(a, obj, fn);
    console.log(a + obj.b + fn.c);  // output: 6

```
> 首先test传递进去的实参中，a是基本类型（复制了一份值），obj是object（指向地址呼啦啦，你动我也动呢），
> fn也当然不是基本类型啦。在执行test的时候，x被赋值为4(跟a没关系，各玩各的嘛，a仍然为1)，y的b被赋值为5，那obj的b也变为5，z的c变为6，那fn的c当然也会是6. 所以结果应该是1+5+6 =12. （其实test不返回z也一样，z仍然改变的）。  
> 参考: http://www.2cto.com/kf/201401/273413.html



#### 关于类型的介绍

- 基本类型
 
Javascript的基本类型有Boolean,String,Number,还有Undefined和Null，首先得明白的是，只有字面量的Boolean，String和Number，以及undefined+null(exact undefined null，区分大小写)才是属于基本类型的，new出来的不算。
 
即：
```js
alert(typeof false);    //"boolean"
var b = new Boolean(false);
alert(typeof b);        //"object"
```
同样，` new String('aaa') `这种都不是基本类型，直接的` a='aaa' `，a是基本类型，这就是：字面量的才是基本类型。
 
另一方面，Object，Function，Array其实都是构造函数，因为可以直接new Object()等，
所以它们都是函数，` (Object instanceof Function === true) && (Function instanceof Object===true)`


### 小结:
> 基本类型, JavaScript 按值传递,copy副本,与原值互不影响  
> 非基本类型，JavaScript将引用 按值传递, 