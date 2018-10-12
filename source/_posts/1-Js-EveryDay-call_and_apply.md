---
title: 每天学点js-call和apply
comments: true
date: 2016-04-13 20:30
categories: [js]
tags: [每天学点js,js]
keywords: js call和apply
---

### apply & call 
- call方法:   `call([thisObj[,arg1[, arg2[, [,.argN]]]]])` 
- apply方法： `apply([thisObj[,argArray]])`


在 javascript 中，call 和 apply 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。

JavaScript 的一大特点是，函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。

```javascript
function fruits() {}
  
fruits.prototype = {
    color: "red",
    say: function() {
        console.log("My color is " + this.color);
    }
}
  
var apple = new fruits;
apple.say();    //My color is red

```
但是如果我们有一个对象`banana= {color : "yellow"}` ,我们不想对它重新定义 say 方法，那么我们可以通过 call 或 apply 用 apple 的 say 方法：

```javascript
banana = {
    color: "yellow"
}
apple.say.call(banana);     //My color is yellow
apple.say.apply(banana);    //My color is yellow
```

所以，可以看出 call 和 apply 是为了动态改变 this 而出现的，当一个 object 没有某个方法（本栗子中banana没有say方法），但是其他的有（本栗子中apple有say方法），我们可以借助call或apply用其它对象的方法来操作。

### 实现继承

1. 单继承  
```javascript
    function Animal(name){      
        this.name = name;      
        this.showName = function(){      
            console.log(this.name);      
        }      
    }      
        
    function Cat(name){    
        Animal.call(this, name);    
    }      
        
    var cat = new Cat("Black Cat");   
    cat.showName();   // Black Cat

```

2. 多重继承

```javascript
    function Sub()  {  
        this.showSub = function(a,b)  {  
            console.log(a-b);  
        }  
    }  
      
    function Add()  {  
        this.showAdd = function(a,b)  {  
            console.log(a+b);  
        }  
    }  
      
    function MyMath()  {  
        Sub.call(this);  
        Add.call(this);  
    } 
    var _math = new MyMath();
    _math.showSub(10, 5);  // output: 5
    _math.showAdd(10, 5);  // output: 15
```

3. 混合使用原型链实现继承
> 参考: [w3school](http://www.w3school.com.cn/js/pro_js_inheritance_implementing.asp)  

```javascript
function ClassA(sColor) {
    this.color = sColor;
}

ClassA.prototype.sayColor = function () {
    console.log(this.color);
};

function ClassB(sColor, sName) {
    ClassA.call(this, sColor);
    this.name = sName;
}

ClassB.prototype = new ClassA();

ClassB.prototype.sayName = function () {
    console.log(this.name);
};

/********测试************/
var objA = new ClassA("blue");
var objB = new ClassB("red", "John");
objA.sayColor();    //输出 "blue"
objB.sayColor();    //输出 "red"
objB.sayName();     //输出 "John"

console.log(objA instanceof ClassA);    // true
console.log(objA instanceof ClassB);    // false
console.log(objB instanceof ClassA);    // true
console.log(objB instanceof ClassB);    // true
```


### 深入理解运用apply、call

  定义一个 log 方法，让它可以代理 console.log 方法，常见的解决方法是：
    ```javascript
    function log(msg)　{
      console.log(msg);
    }
    log(1);    //1
    log(1,2);    //1
    ```

  上面方法可以解决最基本的需求，但是当传入参数的个数是不确定的时候，上面的方法就失效了，这个时候就可以考虑使用 apply 或者 call，注意这里传入多少个参数是不确定的，所以使用apply是最好的，方法如下：
    ```javascript
    function log(){
      console.log.apply(console, arguments);
    };
    log(1);    //1
    log(1,2);    //1 2
    ```

  接下来的要求是给每一个 log 消息添加一个"(app)"的前辍，比如：
    ```
    log("hello world");    //(app)hello world
    ```
  该怎么做比较优雅呢?这个时候需要想到arguments参数是个伪数组，通过 Array.prototype.slice.call 转化为标准数组，再使用数组方法unshift，像这样：
    ```javascript
    function log(){
      var args = Array.prototype.slice.call(arguments);
      args.unshift('(app)');
      
      console.log.apply(console, args);
    };
        ```

### apply、call、bind比较

那么 apply、call、bind 三者相比较，之间又有什么异同呢？何时使用 apply、call，何时使用 bind 呢。简单的一个栗子：

```javascript
var obj = {
    x: 81,
};
  
var foo = {
    getX: function(a, b) {
        console.log('test:', a, b);
        return this.x;
    }
}
  
console.log(foo.getX.bind(obj)('a', 'b'));  
console.log(foo.getX.call(obj, 'a', 'b'));   
// apply 与call的区别在于前者使用数组的方式传参 
console.log(foo.getX.apply(obj, ['a', 'b'])); 
```

三个输出的都是81，但是注意看使用 bind() 方法的，他后面多了对括号。


也就是说，区别是，当你希望改变上下文环境之后并非立即执行，而是回调执行的时候，使用 bind() 方法。而 apply/call 则会立即执行函数。

**再总结一下:**
 - apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；
 - apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；
 - apply 、 call 、bind 三者都可以利用后续参数传参；
 - bind 是返回对应函数，便于稍后调用；apply 、call 则是立即调用 。



### apply传参方式的应用

对于纯数字数组，使用内置函数Math.max()和Math.min()的方法。

内置函数Math.max()和Math.min()可以分别找出参数中的最大值和最小值。
```js
Math.max(1, 2, 3, 4); // 4
Math.min(1, 2, 3, 4); // 1
```
这些函数对于数字组成的数组是不能用的。但是，这有一些类似地方法。

Function.prototype.apply()让你可以使用提供的this与参数组成的_数组(array)_来调用函数。

```js
var numbers = [1, 2, 3, 4];
Math.max.apply(null, numbers) // 4
Math.min.apply(null, numbers) // 1
```
给apply()第二个参数传递numbers数组，等于使用数组中的所有值作为函数的参数。

一个更简单的，基于ES2015的方法来实现此功能，是使用展开运算符.  
```js
var numbers = [1, 2, 3, 4];
Math.max(...numbers) // 4
Math.min(...numbers) // 1
```



#### 参考
> [http://www.admin10000.com/document/6711.html](http://www.admin10000.com/document/6711.html)  
> [https://cnodejs.org/topic/56a050ac8392272262331d62](https://cnodejs.org/topic/56a050ac8392272262331d62)  
> [http://uule.iteye.com/blog/1158829](http://uule.iteye.com/blog/1158829)