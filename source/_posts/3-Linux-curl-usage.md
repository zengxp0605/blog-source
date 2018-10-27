---
title: curl命令的使用
comments: true
date: 2018-4-4 20:46
categories: [Linux]
tags: [Linux]
keywords: Linux命令 curl 
---

## GET 请求

- 获取网站首页  -s 表示静默  --head 表示取得head信息
```
curl  -s  --head  www.jasonzeng.top:8082
# 或者
curl -I www.jasonzeng.top:8082
```

- 显示全部信息
``` 
curl -i www.jasonzeng.top:8082
```

- 显示get请求全过程解析
``` 
curl -v www.jasonzeng.top:8082
```

- 传递参数
> php服务端 $_GET获取, 或者 $_REQUEST
```
curl -s "www.jasonzeng.top:8082/?a=1&b=2&c=3"
```

## POST 请求
> php服务端 $_POST获取, 或者 $_REQUEST
- 方法1: 通过query字符串拼接
```
curl -s -d"a=1&b=2&c=3" www.jasonzeng.top:8082
```

- 方法2: 发送json 字符串 (测试时服务端$_POST并未获取到数据?)
> 需要通过 json_decode(file_get_contents("php://input")) 获取到数据
```
curl -H "Content-type: application/json" -X POST -d '{"user": "admin", "passwd":"12345678"}' www.jasonzeng.top:8082

# 或者
curl -sd'{"a": "a234"}' www.jasonzeng.top:8082
```