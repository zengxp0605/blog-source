---
title: Nginx 配置文件中通过正则匹配域名
comments: true
date: 2016-5-11 15:07
categories: [Nginx]
tags: [Nginx]
keywords: nginx
---

### 访问 http://test12.demo.com  ==> 对应路径到 /data/httpd/test12/www/   
```
server
{
	listen  80;
	server_name ~test(?<num>(\d+)?).demo.com;
	root  /data/httpd/test$num/www;
	index index.php index.html index.htm;

	include	common.conf;
	location ~ .*\.php?$
	{
		include fcgi.conf;
		fastcgi_pass  127.0.0.1:9000;
		fastcgi_index index.php;
		fastcgi_intercept_errors on;
	}
}
```