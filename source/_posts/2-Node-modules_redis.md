---
title: Nodejs redis模块使用
comments: true
date: 2016-4-14 17:00
categories: [nodejs]
tags: [nodejs,redis]
keywords: Nodejs redis模块
---

### 1. 通用命令

```javascript
    var redis = require('redis').createClient(6379,'127.0.0.1');

    redis.info(function(err, response){
        //console.log(err, response);
    });

    // 普通命令set/ get
    redis.set('aaa', 111111);
    redis.get('aaa', function(err, reply){
        console.log(reply);
    });

    // 添加过期时间
    redis.set('key', 'datas exprie 5 minute', 'EX', 60 * 5, function(err, rs){
        console.log('-----------------------------');
        console.log(err, rs);
        console.log('-----------------------------');
    });

```

### 2. 简述node_redis 模块 `publish/subscribe` 的使用

#### 服务端发布 publish

```javascript
    var redis = require('redis').createClient(6379,'127.0.0.1');
    
    // 发布频道
    redis.publish('first', '【first】: news 1');
    redis.publish('first', '【first】: news 2');
    redis.publish('second','【second】： test ...');

    // 3秒后自动退出发布
    setTimeout(function(){
        console.log('quit publish');
        redis.quit();
    }, 3000);

```

#### 客户端订阅 

```javascript
    // 订阅频道
    //  subscribe first second

    var client = require('redis').createClient(6379,'127.0.0.1');
    var msg_count = 0;
     
    client.on("subscribe", function (channel, count) {
        console.log('client: 订阅了频道-' , channel, ' |当前频道数量', count);
    });


    client.on('message', function (channel, message) {
        console.log("Sub channel " + channel + ": " + message);
        msg_count ++;
        if (msg_count === 3) {
            console.log('收到3条消息, 自动退出订阅!');
            client.unsubscribe();
            client.quit();
        }
    });

    client.subscribe('first', 'second');

```