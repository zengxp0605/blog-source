---
title: Nodejs q模块使用1
comments: true
date: 2016-4-14 18:00
categories: [nodejs]
tags: [nodejs,promise]
keywords: nodejs promise简单使用
---

## promise简单使用

#### 链式操作

```javascript
  var Q = require('q');
  var processChat = function(c){
     return c.split('');
  }
 
  var processArr = function(arr){
     return arr.join(' || ');
  }
  Q.fcall(function(){
     return 'aaa';
  }).then(processChat).then(processArr)    // 连续处理上一个方法中返回的 'aaa'
  .then(function(rs){
     console.log('rs: ' , rs);      //  rs:  a || a || a
  })
  .done();

```

#### all 的写法  
```javascript
    var Q = require('q');
    function eventually(value) {
        return Q.delay(value, 2100); // === Q(value).delay(2000);
    }

    Q.all([1, 2, 3].map(eventually))
    .done(function (result) {
        console.log(result);
    });

    Q.all([
        eventually(10),
        eventually(20)
    ])
    .spread(function (x, y) {
        console.log(x, y);
    })
    .done();
```

#### all 的作用: 所有的promises 执行完成后返回结果(array)

```javascript
var Q = require('q');

var testFun1 = function(v){
    return Q.delay(3000).then(function(){
        return v  + 1;
    });
};

var testFun2 = function(v){
    return v * 2;
};

var errFun = function(v){
    return Q.delay(5000).then(function(){
        throw new Error('Test Error!');
    });
};

Q.all([
    testFun1(10),
    testFun2(20),
    //errFun(30),
])
.spread(function (x, y) {
    console.log(x, y);
    return Q.delay('test msg', 5000);
}).then(function(rs){
    console.log('delay 5s message: '+ rs);
})
.catch(console.error)
.done(function(){
    console.log('done....');
    clearInterval(timer);  // 执行完毕清除倒计时
});

// 计时器,用于显示效果
var count = 0;
var timer = setInterval(function(){
    console.log(++ count);
}, 1000);
 
```

### any 

any 的作用是 promises 数组中,哪个方法先执行完就返回该方法的结果, 只有所有的返回都是rejected 才会rejected  
`[Error: Can't get fulfillment value from any promise, all promises were rejected.]`  
如果有提早抛出错误, 则进入catch 代码段

```javascript
    var Q = require('q');

    var testFun1 = function(v){
        var deferred = Q.defer();
        Q.delay(3000).then(function(){
            // 这里如果抛出错误, 会由后面的fail捕获
            //throw new Error('someErr!');
            //deferred.reject('Test Error1');
            deferred.resolve(v + 1);
        }).fail(function(err){
            console.log('{testFun1},error: ' + err);
        }).done();
        return deferred.promise;
    };

    var testFun2 = function(v){
        var deferred = Q.defer();
        Q.delay(2000).then(function(){
            //deferred.reject('Test Error2');
            deferred.resolve(v * 2);
        }).done();
        return deferred.promise;
    };

    var errFun = function(v){
        var deferred = Q.defer();
        Q.delay(1000).then(function(){
            deferred.reject('Test Error3');
        }).done();
        return deferred.promise;
    };

    Q.any([
        testFun1(10),
        testFun2(20),
        errFun(30),
    ])
    .then(function (x) {
        console.log(x);
    }).catch(console.info)
    .done();

```


#### delay 延时操作, 封装 `setTimeout`
 - 下面的两种写法是一样的

```javascript
    var value = 'value string...';
    Q(value).delay(2000).done(function(rs){
        console.log(rs);  // 这里会输出 value
    });

    Q.delay(2000).then(function(){
        console.log('origin string');
    });

```
 - 还可以使用下面的写法  
```javascript
    function eventually(value) {
        return Q.delay(value, 2100); // === Q(value).delay(2000);
    }

    eventually('My name is Jason').then(function(rs){
        console.log(rs);
    }).done();
```


## 错误处理  
**then 后面一定要有done, 或者加上catch/fail **

```javascript
    Q.fcall(function(){
       return 1;
    })
    .then(function(){
        console.log(111, a);
    })
    //.done();   // 这里的done 一定要写,或者加上catch/fail, 否则错误将会湮灭, 无法追踪

```

**如果是需要返回promise, 供别人继续then 调用的话, 则不可以写done, 可以有cacth/fail **


