---
title: 每天学点js-js类型判断(typeof,instanceof,constructor,prototype)
comments: true
date: 2016-7-8 19:30
categories: [js]
tags: [每天学点js,js]
keywords: js类型判断
---


### 定义的数据

```js
var _undefined = undefined;
var _null = null;
var _date = new Date();

var _number = 123.00;  // JavaScript 只有一种数字类型。数字可以带小数点，也可以不带：
var _numberObj = new Number(123);

var str = 'a string';
var strObj = new String('a new string'); 

var bool = true;
var boolObj = new Boolean(true);

var arr = [1, 2, 3];
var arrObj = new Array(1,2,3);

var obj = {a: 111, b: 222};

var fun = function(){};
var funObj = new Function('alert(111)');

```
<!--more-->

### typeof

```js

console.log(
    _undefined,
    typeof _undefined === 'undefined' // output: true
);
console.log('-----------------------------');

console.log(
    _null,
    typeof _null === 'object' // output: true
);
console.log('-----------------------------');

console.log(
    _date,
    typeof _date === 'object' // output: true
);
console.log('-----------------------------');

console.log(
    _number,
    typeof _number === 'number' // output: true
);
console.log('-----------------------------');

console.log(
    _numberObj,
    typeof _numberObj === 'object' // output: true
);
console.log('-----------------------------');

console.log(
    str,
    typeof str === 'string' // output: true
);
console.log('-----------------------------');

console.log(
    strObj,
    typeof strObj === 'object' // output: true
);
console.log('-----------------------------');

console.log(
    bool,
    typeof bool === 'boolean' // output: true
);
console.log('-----------------------------');

console.log(
    boolObj,
    typeof boolObj === 'object' // output: true
);
console.log('-----------------------------');

console.log(
    arr,
    typeof arr === 'object' // output: true
);
console.log('-----------------------------');

console.log(
    arrObj,
    typeof arrObj === 'object' // output: true
);
console.log('-----------------------------');

console.log(
    obj,
    typeof obj === 'object' // output: true
);
console.log('-----------------------------');

console.log(
    fun,
    typeof fun === 'function' // output: true
);
console.log('-----------------------------');

console.log(
    funObj,
    typeof funObj === 'function' // output: true
);
console.log('-----------------------------');


```


### constructor

```js

// console.log(
//     _undefined,
//     _undefined.constructor.name  // output: 报错
// );
// console.log('-----------------------------');

// console.log(
//     _null,
//     _null.constructor.name  // output: 报错
// );
// console.log('-----------------------------');

console.log(
    _date,
    _date.constructor.name === 'Date' // output: true
);
console.log('-----------------------------');

console.log(
    _number,
    _number.constructor.name === 'Number'  // output: true
);
console.log('-----------------------------');

console.log(
    _numberObj,
    _numberObj.constructor.name === 'Number' // output: true
);
console.log('-----------------------------');

console.log(
    str,
    str.constructor.name === 'String' // output: true
);
console.log('-----------------------------');

console.log(
    strObj,
    strObj.constructor.name === 'String'  // output: true
);
console.log('-----------------------------');

console.log(
    bool,
    bool.constructor.name === 'Boolean'  // output: true
);
console.log('-----------------------------');

console.log(
    boolObj,
    boolObj.constructor.name === 'Boolean'  // output: true
);
console.log('-----------------------------');

console.log(
    arr,
    arr.constructor.name === 'Array'  // output: true
);
console.log('-----------------------------');

console.log(
    arrObj,
    arrObj.constructor.name === 'Array'  // output: true
);
console.log('-----------------------------');

console.log(
    obj,
    obj.constructor.name === 'Object'  // output: true
);
console.log('-----------------------------');

console.log(
    fun,
    fun.constructor.name === 'Function'  // output: true
);
console.log('-----------------------------');

console.log(
    funObj,
    funObj.constructor.name === 'Function' // output: true
);
console.log('-----------------------------');

```

### prototype
```js

console.log(
    _undefined,
    Object.prototype.toString.call(_undefined) === '[object Undefined]'  // output: true
);
console.log('-----------------------------');

console.log(
    _null,
    Object.prototype.toString.call(_null) === '[object Null]'  // output: true
);
console.log('-----------------------------');

console.log(
    _date,
    Object.prototype.toString.call(_date) === '[object Date]'  // output: true
);
console.log('-----------------------------');

console.log(
    _number,
    Object.prototype.toString.call(_number) === '[object Number]'   // output: true
);
console.log('-----------------------------');

console.log(
    _numberObj,
    Object.prototype.toString.call(_numberObj) === '[object Number]'  // output: true
);
console.log('-----------------------------');

console.log(
    str,
    Object.prototype.toString.call(str) === '[object String]'  // output: true
);
console.log('-----------------------------');

console.log(
    strObj,
    Object.prototype.toString.call(strObj) === '[object String]'   // output: true
);
console.log('-----------------------------');

console.log(
    bool,
    Object.prototype.toString.call(bool) === '[object Boolean]'   // output: true
);
console.log('-----------------------------');

console.log(
    boolObj,
    Object.prototype.toString.call(boolObj) === '[object Boolean]'   // output: true
);
console.log('-----------------------------');

console.log(
    arr,
    Object.prototype.toString.call(arr) === '[object Array]'   // output: true
);
console.log('-----------------------------');

console.log(
    arrObj,
    Object.prototype.toString.call(arrObj) === '[object Array]'   // output: true
);
console.log('-----------------------------');

console.log(
    obj,
    Object.prototype.toString.call(obj) === '[object Object]'   // output: true
);
console.log('-----------------------------');

console.log(
    fun,
    Object.prototype.toString.call(fun) === '[object Function]'   // output: true
);
console.log('-----------------------------');

console.log(
    funObj,
    Object.prototype.toString.call(funObj) === '[object Function]'  // output: true
);
console.log('-----------------------------');


```

```js
// 获取数据类型
var getType = function (obj) {
    var _type = Object.prototype.toString.call(obj);
    return _type.slice(8, -1);
}

```

### 继承时的 instanceof 和 constructor
```js
class A {
    msg() {
        console.log('message');
    }
}

class B extends A {

}

var a = new A();
var b = new B();

/******************* instanceof ****************************/
console.log(a instanceof A); // true
console.log(a instanceof B); // false

console.log(b instanceof A); // true
console.log(b instanceof B); // true


/******************* constructor ****************************/
console.log(a.constructor === A);  // true
console.log(a.constructor === B);  // false

console.log(b.constructor === A);  // false
console.log(b.constructor === B);  // true


/******************* prototype ****************************/
console.log(
    Object.prototype.toString.call(A),  // '[object Function]'
    Object.prototype.toString.call(B),  // '[object Function]'
    Object.prototype.toString.call(a),  // '[object Object]'
    Object.prototype.toString.call(b)   // '[object Object]'
);

console.log(
    typeof A,  // function
    typeof B,  // function
    typeof a,  // object
    typeof b   // object
);
```





### 小结:

1. typeof 
- 结果有: undefined,string,number,boolean,object,function
- 注意字面量和 new赋值导致的typeof 不相同 ==> (string, number, boolean)
 
2. constructor
- 除了null和undefined 不能用于判断之外,其余的数据类型都比较符合预期
- 有继承的情况未做验证, 会出现不正确的情况

3. instanceof
- `xxx instanceof Object` 这个判断将适用于大部分的类型
- 数字,字符串,布尔值 字面量无法判断
- 有继承的情况未做验证, 会出现不正确的情况

4. prototype
- 无继承的情况下判断结果和 constructor 一致
- 注意判断时的大小写!!

