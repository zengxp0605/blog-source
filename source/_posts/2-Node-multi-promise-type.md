---
title: nodejs 多种promise库混用的写法
comments: true
date: 2016-11-16 20:13:54
categories: [nodejs]
tags: [nodejs]
keywords: nodejs promise q bluebird
---

## 说明

- 由于一些老的项目使用`Q`模块封装promise, 现在使用新的`bluebird`做的项目需要使用到原来封装的库,这时有些代码可能会出现无法兼容的问题,  
可以将原来的Q封装转换为 `bluebird`

- 切换promise库的原因是Q比`bluebird`速度慢,并且内存占用较大,最重要的是Q的promise链在没有`.catch` 也没有 `.done`时,  
 内部抛出的错误没有任何提示,容易导致内存飙升, 目前6.x版本原生的Promise对象速度也比较快的,但是没有捕获的错误也是没有提示,而`bluebird`会有错误提示
 `Unhandled rejection...`


### 以Q开头的Promise链, 可以以Q的方式结束
```js
const Promise = require('bluebird');
const Q = require('q');

function q1() {
    var deferred = Q.defer();
    if (Math.random() < 0.5) {
        deferred.reject('q1 test err');
    } else {
        deferred.resolve(true);
    }
    return deferred.promise;
}

function b1() {
    if (Math.random() < 0.5) {
        return Promise.resolve(1);
    } else {
        return Promise.reject('b1 test err');
    }
}

q1().then((rs) => {
    console.log('q1 rs: ', rs);
    return b1();
}).then((rs) => {
    console.log('b1 rs: ', rs);
}).fail(err => {
    console.log('err: ', err);
}).done();

```

### 以`bluebird`开头的Promise链, 则以`bluebird`的方式结束(bluebird没有提供 .fail方法,并且不建议使用.done)

- 这里没有捕获错误, 但是无论`b1`,`q1`方法抛出错误后,bluebird 会有错误提示

```js
b1().then((rs) => {
    console.log('b1 rs: ', rs);
    return q1();
}).then((rs) => {
    console.log('q1 rs: ', rs);
});

```