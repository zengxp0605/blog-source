---
title: ngnix通过cookie区分环境分流
comments: true
date: 2019-5-11 15:07
categories: [Nginx]
tags: [Nginx]
keywords: nginx
---

## 环境如下
nginx version: nginx/1.18.0
CentOS Linux release 7.6.1810 (Core)

## 目标
使用cookie做分流，给测试人员提供多套测试环境 ，根据前端设置的cookie，nginx再根据cookie实现分流

<!-- more -->

## nginx 增加配置
```config
upstream nttest1{
   server 127.0.0.1:8091;
}
upstream nttest2{
   server 127.0.0.1:8092;
}

server {
    listen 8090;
    server_name localhost;

    index index.html;
    location / {
	 set $group nttest1;
        if ($http_cookie ~* "projId=test2") {
            set $group nttest2;
        }

        proxy_pass http://$group;
    }
}
server {
    listen 8091;
    server_name localhost;

    index index.html;
    location / {
	root /data/httpd/multi-test1;
    }
}
server {
    listen 8092;
    server_name localhost;

    index index.html;
    location / {
        root /data/httpd/multi-test2;
    }
}
```

## reload nginx配置
```sh
/usr/local/nginx/sbin/nginx -s reload
```

## 新建测试html文件
```sh
cd /data/httpd
mkdir multi-test1
mkdir multi-test2
echo "multi-test1" > multi-test1/index.html
echo "multi-test2" > multi-test2/index.html
```

## 本地新增cookie
cookie
```
projId=test2
```

## 实现效果
当cookie中 projId=test2时，
访问 http://xxx.com:8090/ 会转到 8091端口， 显示当html为 multi-test1

当cookie中没有projId，或者projId为其他值时，
访问 http://xxx.com:8090/ 会转到 8092端口， 显示当html为 multi-test2


## 参考
<https://www.cnblogs.com/ExMan/p/12531875.html>