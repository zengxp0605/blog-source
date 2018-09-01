---
title: 一台服务器上启用多个独立网站(nginx,nodejs)
comments: true
date: 2017-08-10 16:18
categories: [nodejs]
tags: [nodejs,nginx]
keywords: nodejs nginx
---

### nginx 配置
```conf
	server {
		listen 80;
		server_name local.b1.com;

		location / {
			proxy_set_header   X-Real-IP $remote_addr;
			proxy_set_header   Host      $http_host;
			proxy_pass         http://127.0.0.1:3002;
		}
	}

	server {
		listen 80;
		server_name local.jasonzeng.com;

		location / {
			proxy_set_header   X-Real-IP $remote_addr;
			proxy_set_header   Host      $http_host;
			proxy_pass         http://127.0.0.1:3003;
		}
	}
```

### nodejs 启动多个站点

- 站点1
```js
var express = require('express');
var app = express();
var port = 3002;

app.get('/', function (req, res) {
    res.send(`Home page,from port:${port},${__filename}`);
});

app.listen(port);
console.log(`server start port ${port}`);
```

- 站点2
```js
var express = require('express');
var app = express();
var port = 3003;

app.get('/', function (req, res) {
    res.send(`Home page,from port:${port},${__filename}`);
});

app.listen(port);
console.log(`server start port ${port}`);
```

### 其他
- 由于域名是随便取的,本地测试需要绑定 hosts
- hosts 文件添加如下配置: (C:\Windows\System32\drivers\etc)
```conf
127.0.0.1  local.b1.com
127.0.0.1  local.jasonzeng.com
```