---
title: Nodejs jwt 简介
comments: true
date: 2017-12-21 22:18
categories: [nodejs]
tags: [nodejs,redis]
keywords: Nodejs  jwt
---

# Nodejs jwt 简介

> JSON Web Token（JWT）是一个非常轻巧的规范。这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的信息。

## JWT的组成

一个JWT实际上就是一个字符串，它由三部分组成，头部、载荷与签名。

### 头部（Header）

```js
var header = {
    "typ": "JWT",
    "alg": "HS256"
};
```

- 使用`base64`编码后:

```js
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
```

### 载荷（Payload）

```js
var payload = {
    "iss": "TEST JWT",
    "exp": 1513864661,
    "aud": "www.example.com",
    "sub": "test@example.com",
    "from_user": "A",
    "to_user": "B"
};

```

- 使用`base64`编码后:

```js
eyJpc3MiOiJURVNUIEpXVCIsImV4cCI6MTUxMzg2NDY2MSwiYXVkIjoid3d3LmV4YW1wbGUuY29tIiwic3ViIjoidGVzdEBleGFtcGxlLmNvbSIsImZyb21fdXNlciI6IkIiLCJ0YXJnZXRfdXN
lciI6IkEifQ
```

### 签名(Signature)

```js
// 这个方法还有问题
function getSign(message = 'my message') {
    const hash = crypto.createHmac('sha256', secret)
        .update(message)
        .digest('hex');
    // console.log('hash: ', hash);
    return hash;
}
```

## 测试代码

```js
const base64url = require('base64url');
const crypto = require('crypto');
const jwt = require('jsonwebtoken');
function toBase64(data) {
    // 使用其他模块
    return base64url(JSON.stringify(data));
}
// console.log(toBase64(header), toBase64(payload), getSign());

var secret = 'my-secret';
var encodedString = toBase64(header) + '.' + toBase64(payload);
var signStr = getSign(encodedString);
var token = encodedString + '.' + signStr;
console.log('token: ', token);

// 这里可以正常的解析出来, 因为header和payload只是base64转码,并未加密
console.log('decode: ', jwt.decode(token));

// 这里无法通过验证?? 
// console.log('verify: ', jwt.verify(token, secret));

```

## 注意事项

- jwt中`payload`的信息是会暴露,所以，在JWT中，不应该在载荷里面加入任何敏感的数据,比如登录密码等
- jwt中`payload`的信息直接通过base64解码即可获得,所以`jwt.decode(token)`不需要`secret`就可以得到数据
- secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和jwt的验证，所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了。


## 客户端通过http header发送token

```js
  fetch('api/user/1', {
    headers: {
        'Authorization': 'Bearer ' + token
    }
 })
```

## 参考: 
> [http://blog.leapoahead.com/2015/09/06/understanding-jwt/](http://blog.leapoahead.com/2015/09/06/understanding-jwt/)   
>  [http://www.jianshu.com/p/576dbf44b2ae](http://www.jianshu.com/p/576dbf44b2ae)
