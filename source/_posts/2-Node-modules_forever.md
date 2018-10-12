---
title: Nodejs forever模块使用
comments: true
date: 2016-4-8 13:53:31
categories: [nodejs]
tags: [nodejs]
keywords: Nodejs forever模块使用
---

### 使用 node-forever 模块进行本地开发

**重点:监控文件夹更新->重启app**

0. 全局安装
```
npm install forever -g
```

1. 首先在任意目录新建apps.json 文件(例 d:/toos/forever),内容如下:

```
{
    "uid":"test",
    "watch":true,
    "append":true,
    "script":"server/index.js",  
    "sourceDir":"d:/httpd/test"
}

```
> 上述json文件表示,启动`d:/httpd/test/server/index.js` 文件, 并监控 `d:/httpd/test` 文件夹的更新
> 如果`d:/httpd/test` 文件夹(含子文件夹)下有更新,则自动重启 `server/index.js` 这个脚本

2. 启动服务
```
# 启动js
forever start index.js

# 启动脚本, 通过json文件配置
forever start apps.json  

# 查看日志, (以日志流的方式输出到控制台)
forever logs 0 -f  # 0表示第0个server
```

3. 查看启动的列表
```
forever list
```

4. 停止脚本,清除日志
```
forever stopall 
forever cleanlogs
```


5. 详见 [github](https://github.com/foreverjs/forever)