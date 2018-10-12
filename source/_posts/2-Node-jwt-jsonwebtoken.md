---
title: Nodejs jsonwebtoken模块的使用
comments: true
date: 2017-12-21 22:18
categories: [nodejs]
tags: [nodejs,redis]
keywords: Nodejs jsonwebtoken jwt
---

# nodejs token 校验机制

> 替换session服务端存储登录态的token方案(nodejs)
> 用户登录后服务端生成`token`返回客户端,客户端存储token到本地,之后的请求都需要通过token校验用户信息

- 使用字符串加盐

```js
let fs = require('fs');
let jwt = require('jsonwebtoken');

function verifyToken(token, secret) {
    try {
        let result = jwt.verify(token, secret);
        let cTime = Math.floor(Date.now() / 1000);
        if (result.data && result.exp > cTime) {
            return result.data;
        }
        return false;
    } catch (error) {
        return false;
    }
}

var secret = 'b9bb3d53a7ca4b78d438d511e015d4';
var token = jwt.sign({
    exp: Math.floor(Date.now() / 1000) + (60 * 60),
    data: '12456',
}, secret);

console.log(jwt.decode(token), jwt.verify(token, secret), 'data: ' + verifyToken(token, secret));

// 不太明白为什么这里的 jwt.decode 不需要secret就能解析出来结果??

```

- 使用ras公钥和私钥

```js
console.log('*************-------利用ras做验证-------****************');

var cert = fs.readFileSync('private.pem');  // 私钥
var token = jwt.sign({ foo: 'bar' }, cert, { algorithm: 'RS256' }); // algorithm默认为 HS256

var pubKey = fs.readFileSync('public.pem');  // 对应的公钥

console.log(jwt.verify(token, pubKey, { algorithm: 'RS256' }));

```

- 私钥生成方法

```bash
$ openssl genrsa -out private.pem 1024  // 私钥生成
$ openssl rsa -in private.pem -pubout -out public.pem  // 生成对应的公钥
```
