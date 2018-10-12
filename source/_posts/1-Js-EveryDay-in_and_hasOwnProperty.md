---
title: 每天学点js-检查某对象是否有某属性
comments: true
date: 2016-04-18 20:30
categories: [js]
tags: [每天学点js,js]
keywords: js检查某对象是否有某属性
---


### 检查某对象是否有某属性


当你需要检查某属性是否存在于一个对象，你可能会这样做：

```javascript
var myObject = {
  name: '[@tips_js](/user/tips_js)'
};

if (myObject.name) { ... }

```
这是可以的，但是你需要知道有两种原生方法可以解决此类问题。in 操作符 和 Object.hasOwnProperty，任何继承自Object的对象都可以使用这两种方法。


#### 看一下较大的区别

```javascript
var myObject = {
  name: '[@tips_js](/user/tips_js)'
};

myObject.hasOwnProperty('name'); // true
'name' in myObject; // true

myObject.hasOwnProperty('valueOf'); // false, valueOf 继承自原型链
'valueOf' in myObject; // true
```

两者检查属性的深度不同，换言之hasOwnProperty只在本身有此属性时返回true,而in操作符不区分属性来自于本身或继承自原型链。

这是另一个例子

```javascript
var myFunc = function() {
  this.name = '[@tips_js](/user/tips_js)';
};
myFunc.prototype.age = '10 days';

var user = new myFunc();

user.hasOwnProperty('name'); // true
user.hasOwnProperty('age'); // false, 因为age来自于原型链

```
