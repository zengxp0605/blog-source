---
title: Nginx 配置https
comments: true
date: 2017-5-11 15:07
categories: [Nginx]
tags: [Nginx]
keywords: nginx
---

## 申请免费证书
@ <https://console.cloud.tencent.com/ssl>


## nginx配置https

```conf
server {
    listen 80;
    listen  [::]:80;
    listen       443 ssl;
    listen      [::]:443 ssl;
    server_name  www.jasonzeng.top;

    ssl_certificate      /data/crt-key/1_www.jasonzeng.top_bundle.crt;
    ssl_certificate_key  /data/crt-key/2_www.jasonzeng.top.key;

    #ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;
    ssl_prefer_server_ciphers on;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;

    # docker 转发
    location /docker {
        proxy_pass http://127.0.0.1:8080/;
        proxy_redirect  off;
    }

    location / {
        root   /data/httpd/blog/html;
        index  index.html index.htm;
    }
}
```

## 参考
> [Nginx 服务器证书安装](https://cloud.tencent.com/document/product/400/35244)