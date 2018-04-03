---
title: nodejs primus启动https服务
comments: true
date: 2017-12-18 13:45
categories: [nodejs]
tags: [nodejs]
keywords: nodejs primus https
---


## 使用primus时,以https的方式启动
- 使得服务器可以同时提供primus 的socket链接以及http协议


<!--more-->

#### `server.js`代码如下

```js
var Primus = require('primus');
var express = require('express');
var fs = require('fs');
var app = express();

var opts = {
    PORT: '8102',
    NEED_HTTPS: 1,
    HTTPS_CONFIG: {
        'ROOT': __dirname + '/cret/',
        'CERT': 'certificate.pem',
        'KEY': 'privatekey.pem',
        'CA': '', // 这个ca是什么
    }
};

var httpsOptions = {};
var server;

if (opts.NEED_HTTPS) {
    console.log('https');
    httpsOptions.root = opts.HTTPS_CONFIG.ROOT;
    httpsOptions.cert = fs.readFileSync(opts.HTTPS_CONFIG.ROOT + opts.HTTPS_CONFIG.CERT);
    httpsOptions.key = fs.readFileSync(opts.HTTPS_CONFIG.ROOT + opts.HTTPS_CONFIG.KEY);
    // httpsOptions.ca = fs.readFileSync(opts.HTTPS_CONFIG.ROOT + opts.HTTPS_CONFIG.CA);
    server = require('https').createServer(httpsOptions, app);
} else {
    console.log('http');
    server = require('http').createServer(app);
}

server.listen(opts.PORT, function () {
    console.log('Server listening on port ' + opts.PORT);
});

app.get('/', function (req, res) {
    res.send('hello world<br/>' + Math.random());
});

app.all('/api/:name', function (req, res) {
    res.send(`Name: ${req.params.name} <br/>` + Math.random());
});

// 创建 primus 通讯模块实例
// primus 通讯服务配置。
let primusOpts = {
    port: opts.PORT,
    transformer: opts.WSEngine || 'websockets',
    transport: {
        noDelay: true
    },
    compression: true,
    strategy: false,
    iknowhttpsisbetter: false,
    //pingInterval:false // 设置心跳检查时间ms，false为关闭 
};

var primus = new Primus(server, primusOpts);;

primus.on('connection', (spark) => {
    console.log('----connection: ', spark, Date.now());
});

// 监听 primus 通讯模块初始化事件
primus.on('initialised', () => {
    console.log(`Load Primus on port ${opts.PORT}`);
});


```

## 启动 `node server.js`

## 浏览器打开
- https://localhost:8102/api/getInfo
- http://localhost:8102/primus/primus.js


> https证书生成,参考: [http://blog.fens.me/nodejs-https-server/](http://blog.fens.me/nodejs-https-server/)

