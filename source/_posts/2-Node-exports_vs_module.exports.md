---
title: exports和module.exports的理解
comments: true
date: 2016-4-5 12:26:24
categories: [nodejs]
tags: [nodejs]
keywords: exports和module.exports的理解
---

#### 要理解exports和module.exports,首先应该了解一下Node中js模块的编译过程

> 朴灵在《深入浅出node.js》一书 “Javascript模块编译”小节中介绍:  
在编译的过程中,Node对获取的JavaScript文件内容进行了头尾包装,在头部添加了
`(function(exports, require, module, __filename, __dirname){ \n`, 在尾部添加了 `\n});` ;

  
由此可以知道,我们代码中写的一系列方法,如:  
```
exports.add = function(a, b){
    return a + b;
};

exports.sub = function(a, b){
    return a - b;
};
```
会被包装成:  
```
(function(exports, require, module, __filename, __dirname){
    exports.add = function(a, b){
        return a + b;
    };

    exports.sub = function(a, b){
        return a - b;
    };
})(module.exports, require, module, module.filename, currentDirname);
// 这里传入的参数是我个人的理解
```
来执行的,也就更好的理解 **exports就是module.exports的引用** 这个说法了;
  
  
所以,如果直接给exports赋值而不是添加属性的话,是无法正常导出方法的,如model.js定义为:
```
// 此时 exports指向了新的内存块, 与 module.exports 的引用关系断裂
exports = {
    add: function(){},
    sub: function(){},
};
```

```
var m = require('./model');
console.log(m); // 这里m 是空对象
console.log(typeof m.add); // undefined

```
以上直接给exports赋值无法导出的原因是: 
> exports对象是通过形参的方式传入的,直接赋值给形参会改变形参的引用,但是并不能改变作用域外的值.   
  
具体参看文章最后<a href="#fun">js对象引用</a>
  
  

#### 以下写法是两种方法是可行的: 

**1. 使用exports逐个添加属性**
```
exports.add = function(a, b){
    return a + b;
};

exports.sub = function(a, b){
    return a - b;
};
```
  
**2. 新建对象,最后将对象赋值给 module.exports**
```
var obj = {
    id: 'test',
    add: function(){},
    sub: function(){},
};


module.exports = obj;

```
或者直接给 module.exports赋值一个对象
```
module.exports = {
    id: 'test',
    add: function(){},
    sub: function(){},
};

```

对于很多人使用的 exports = module.exports = someObject, 个人觉得有些多余了,这样赋值后,以下两种方式都无法使用
```
var someObject = {
    add: function(){},
    sub: function(){},
};

exports.test = function(){};  // 无法导出此方法

module.exports.foo = function(){};  // 无法导出此方法

someObject.fun = function(){}; // 只能这么使用 

exports = module.exports = someObject;
```
  
  

#### 最后,再简要说明下 <a href="#fun" name="fun" id="fun">js调用函数时对象传参</a>

```
'use strict';
var globalModule = {
    scope : 'global',
    exports : {
        scope : 'global',
        fun : function(){console.log('globalModule.exports.fun');},
    },
};

(function(exports, module){
    exports.test = 'my-test';   // 可以给引用增加属性
    exports.scope = 'global-mine';  // 也可以修改属性
    console.log(exports);  // output: Object {scope: "global-mine", test: "my-test", ...}
    // 直接赋值给形参会改变形参的引用, 使其指向新的内存块
    // 这里只要进行其它初始化的操作都会导致同样的效果
    exports = {
        scope : 'my-module',
    };
    console.log(exports); // output: Object {scope: "my-module"}

    console.log(module.exports); // output: Object {scope: "global-mine", test: "my-test", ...}

})(globalModule.exports, globalModule);

```

##### 以及关于引用对象的补充示例说明:
 - 调用函数时,通过参数传递对象时,传递的即是该对象的引用, 也就是说形参是实参的引用,它们指向同一内存地址

```
var a = {name: 'aaa'};
var b = a; // 这里 b 即是 a 的引用,它们指向同一内存地址

console.log(a);     // output: Object {name: "aaa"}
console.log(b);     // output: Object {name: "aaa"}

b.name = 'aaabbb';   // 这里改变了同一个内存地址中的值,因此下面两行输出仍然一致
console.log(a);     // output: Object {name: "aaabbb"}
console.log(b);     // output: Object {name: "aaabbb"}

var b = {name: 'bbb'};   // 这里b引用了一个新的对象, 即指向了另一个内存地址
console.log(a);     //  这里a没有被上一赋值改变,仍指向原来的内存地址 output: Object {name: "aaabbb"}
console.log(b);     // 这里b是新的内存指向, output: Object {name: "bbb"}
```


#### 参考
- [exports 和 module.exports 的区别](https://cnodejs.org/topic/5231a630101e574521e45ef8)
- 朴灵 <<深入浅出node.js>>