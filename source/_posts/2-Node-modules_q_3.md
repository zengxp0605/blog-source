---
title: Nodejs 使用Q.all时遇到的一个问题
comments: true
date: 2016-7-5 19:00
categories: [nodejs]
tags: [nodejs,promise]
keywords: nodejs qall
---

#### 问题:
- 使用Q.all 获取多个异步函数的结果, 其中有一个函数可能会比较费时(getInfoFromApi), 另外一个返回很快(getStatusFromRedis), 而且这个值是会被其他用户改变的
遇到的情况正好是: `getStatusFromRedis`已经获取到了数据,等待api返回数据的时候, 其他操作改变了redis的值, 而后续的判断仍然使用了原来取出来的值,因此导致判断这个值时出现错误!!

- 代码如下:

```js
var Q = require('q');

var redisValue = 0;

// 费时的api操作
function getInfoFromApi() {
    var deferred = Q.defer();

    Q.delay(35000).then(function() {
        console.log('delay 35000,redisValue: ' + redisValue);
        deferred.resolve({
            test: 'data'
        });
    }).done();

    return deferred.promise;
}

function changeRedisVal() {
    redisValue = 1;
    console.log('changeRedisVal,redisValue: ' + redisValue);
}

function getStatusFromRedis(){
    return redisValue;
}

Q.all([
    getInfoFromApi(),  // 可能费时的操作

    getStatusFromRedis(),  // 获取一个会被其他操作改变的状态值

]).spread(function(info, status) {
    console.log('done....', info, 'status: ' + status);  // 这里获取的status值是未被改变的值
}).done();

// 其他操作
Q.delay(10000).then(function() {
    console.log('delay 1000');
    changeRedisVal();
}).done();

```


- 简单分析就会发现这用法其实是错误的, 不应该使用all获取,应该先获取费时的数据,再获取需要用于验证并且是可变的数据

```js
Q.fcall(function(){
    return getInfoFromApi();
}).then(function(info){
    console.log(info);
    return getStatusFromRedis();
}).then(function(status){
    console.log('status', status);
})
.catch(console.error)
.done();

```