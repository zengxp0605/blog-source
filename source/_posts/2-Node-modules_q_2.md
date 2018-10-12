---
title: Nodejs q模块使用2
comments: true
date: 2016-4-14 19:00
categories: [nodejs]
tags: [nodejs,promise]
keywords: nodejs promise循环
---

nodejs中promise一直使用的是[Q模块](https://github.com/kriskowal/q),对于循环和递归的实现却折腾了好一会儿才想明白...

### promise 循环  
 - 使用拼接then的方式, arr中的函数将依次按照顺序执行, 最后的 result只能拿到arr中最后一个函数的结果  

```javascript
    var test = {};
    var Q = require('q');
    var _ = require('underscore');
    var tableList = []; // 存储id, 相当于redis 的一个set

    // 测试创建桌子
    test.createTable = function() {

        _getUniqTableId().then(function(result){
            console.log('result: ', result );
            console.log('--------------------------');
            console.log('tableList: ', tableList.join(',') ,' len: ' + tableList.length);
        }).fail(function(error){
            console.log('{test.createTable},error: ' + error);
        }).done(); 
    };
    
    // 获取当前不存在的id
    var _getUniqTableId = function() {
        var arr = [];
        var i = 0;
        while(true){
            if(i++ == 15)  break; // 测试的次数
            (function(_i) {
                var id = _.random(0 ,10);
                arr[_i] = function(){
                    return _isIdExists(id, _i);
                }; 
            })(i);
        }
        var _p = Q(0);
        arr.forEach(function (f) {
            _p = _p.then(f).then(function(rs){
                console.log('rs: ' ,rs);  
                return rs;  
            });
        });
        return _p;
    };

    // 模拟redis 中异步判断id 是否存在于 set中
    // 假设查询需要耗费 500 毫秒
    function _isIdExists(id, _i){   
        var deferred = Q.defer();
        setTimeout(function(){
            var isExists = _.contains(tableList, id);
            if(!isExists){
                tableList.push(id);
            }
            console.log('tableList: ', _i ,'->', tableList.join(','));
            deferred.resolve({id: id, isExists: isExists});  // true 表示存在
        }, 500); 

        return deferred.promise;
    };

    // 调用
    test.createTable();
```

 - 使用Q.all , arr 中的异步方法将同时执行, 所有执行完后返回结果
将上面的 `_getUniqTableId` 方法重写如下: 

```javascript
    var _getUniqTableId = function() {
        var arr = [];
        var i = 0;
        while(true){
            if(i++ == 150)  break; // 测试的次数
            (function(_i) {
                var id = _.random(0 ,10);
                arr[_i] = _isIdExists(id, _i);
            })(i);
        }
        return Q.all(arr);
    };

```

 - 使用 reduce 的写法:  
```javascript
    var _getUniqTableId = function() {
        var arr = [];
        var i = 0;
        while(true){
            if(i++ == 150)  break; // 测试的次数
            (function(_i) {
                var id = _.random(0 ,10);
                arr[_i] = function(){
                    return _isIdExists(id, _i);
                }; 
            })(i);
        }
        var initialVal = 0; // 这个值这里用不到
        return arr.reduce(function (soFar, f) {
            return soFar.then(f).then(function(rs){
                console.log('rs: ' ,rs);
                return rs;
            });
        }, Q(initialVal));
    };
```


 - 通过递归的方式获取tableList中不存在的id,将上面的 `_getUniqTableId` 方法重写如下:  

```javascript
    var _getUniqTableId = function(_count) {
        tableList = [1,2,3,4,5,6,7,8]; // 模拟已经存在的id
        var id = _.random(0, 10);
        return _isIdExists(id).then(function(rs){
            if(rs && rs.isExists != true){
                return id;
            }else{
                _count = !_count  ? 1 : (_count + 1);
                if(_count > 3){  // 最多递归获取3次
                    throw new Error('{_getUniqTableId},获取唯一id的次数超过3次了!!!');
                }
                console.log('重复id: ' + id, ' | count: ' + _count);
                return _getUniqTableId(_count);
            }
        });
    };
```

- **注: 如果这些函数是有前后依赖关系的, 最好使用拼接then 的方式**



### 基础写法

```javascript
    
    var Q = require('q');
    var _ = require('underscore');

    /**
     * 返回是否已锁, true: 已锁, false: 未锁
     */
    function setLock(){
        var isLock = _.random(0, 1);  // 随机值模拟锁
        console.log('isLock: ', isLock);
        var defer = Q.defer();
        if(isLock === 1){
            //throw new Error('It is locked.');
            defer.resolve(true); // 已锁
        } else {
            defer.resolve(false);
        }

        return defer.promise;
    };

    setLock().then(function(isLock){
        if(isLock){
            throw new Error('已锁!');
        }
    })
    .then(function(){
        console.log('test1');
    }, function(err1){
        // 这个then 前面跑出的错误会在这里处理掉, 然后继续执行下面的then
        console.log('err1:', err1);
    })
    .then(function(){
        console.log('test2');
    })
    .then(function(){
        console.log('test3');
        // 这个错误会被catch, 而后面test4 不会执行
        throw new Error('test3-ERR');  
    })
    .then(function(){
        console.log('test4');
    })
    // 下面的catch 和fail 哪个写在前面,error时会执行哪个, done 则都会执行
    .catch(function(err){
        console.log('catch: ', err);
    })
    .fail(function(err){
        console.log('fail: ', err);
    })
    .done(function(){
        console.log('done....');
    });

```



#### 疑问:  
 - fail 和 catch 区别??
 - 如何中断, 不执行后面的then, throw Error 或者then嵌套除外

