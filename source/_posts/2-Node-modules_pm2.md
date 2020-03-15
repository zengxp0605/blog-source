---
title: Nodejs pm2模块使用
comments: true
date: 2016-4-8 13:53:31
categories: [nodejs]
tags: [nodejs]
keywords: Nodejs pm2模块使用
---

### 使用 node-pm2 模块开发


1. 全局安装
```
npm install pm2 -g
```

2. json配置
```
{
  "apps":[
    {
      "name":"pm_test",
      "script":"./server/index.js",
      "cwd":"d:/Tools/nodejs/src/pm2-test",
      "instances":1,
      "watch" : true,
      "error_file":"d:/pm2-logs/errfile.log",
      "out_file":"d:/pm2-logs/outfile.log",
      "autorestart":false
    }
  ]
}

```


#### 其他
```
# 启动 index.js, 名称设置为 `test-name`, 并监控 `D:/Tools/nodejs/src/pm2-test` 文件夹的变化,自动重启
pm2 start  ./server/index.js --watch "D:/Tools/nodejs/src/pm2-test" --name test-name


# 停止
pm2 stop 0 
pm2 stop all   

# 重启
pm2 restart 0      
pm2 restart all 

# 删除
pm2 delete 0 
pm2 delete all //停止并删除所有

# 清除日志
pm2 flush

# 查看日志(通过pm2 list 查看到到服务的id)
pm2 logs 0

# 启动或重启apps.json 中配置的server
pm2 startOrRestart apps.json

# 查看内存使用情况
pm2 monit

```




5. 详见 [github](https://github.com/Unitech/pm2)