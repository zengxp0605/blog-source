---
title: Linux 一些crontab的实例
comments: true
date: 2017-12-6 20:10
categories: [Linux]
tags: [Linux]
keywords: Linux命令
---

## 使用实例

- 每1分钟执行一次command  
```
* * * * * command
```

- 每小时的第3和第15分钟执行  
```
3,15 * * * * command
```

- 在上午8点到11点的第3和第15分钟执行  
```
3,15 8-11 * * * command
```
- 每隔两天的上午8点到11点的第3和第15分钟执行   
```
3,15 8-11 */2 * * command
```

- 每个星期一的上午8点到11点的第3和第15分钟执行   
```
3,15 8-11 * * 1 command
```

- 每晚的21:30重启smb      
```
30 21 * * * /etc/init.d/smb restart
```

- 每月1、10、22日的4 : 45重启smb 
```
45 4 1,10,22 * * /etc/init.d/smb restart
```

- 每周六、周日的1 : 10重启smb
```
10 1 * * 6,0 /etc/init.d/smb restart
```

- 每天18 : 00至23 : 00之间每隔30分钟重启smb 
```
0,30 18-23 * * * /etc/init.d/smb restart
```

- 每星期六的晚上11 : 00 pm重启smb 
```
0 23 * * 6 /etc/init.d/smb restart
```
 
- 每一小时重启smb 
```
* */1 * * * /etc/init.d/smb restart
```
 
- 晚上11点到早上7点之间，每隔一小时重启smb 
```
* 23-7/1 * * * /etc/init.d/smb restart
```
 
- 每月的4号与每周一到周三的11点重启smb 
```
0 11 4 * mon-wed /etc/init.d/smb restart
```
 
- 一月一号的4点重启smb 
```
0 4 1 jan * /etc/init.d/smb restart
```

- 每小时执行/etc/cron.hourly目录内的脚本
```
01  *   *   *   *     root run-parts /etc/cron.hourly
# 说明：
# run-parts这个参数了，如果去掉这个参数的话，后面就可以写要运行的某个脚本名，而不是目录名了
```

- 简单例子
```
每五分钟执行  */5 * * * *
每小时执行    0 * * * *
每天执行      0 0 * * *
每周执行      0 0 * * 0
每月执行      0 0 1 * *
每年执行      0 0 1 1 *
```


> 参考: [http://www.cnblogs.com/peida/archive/2013/01/08/2850483.html](http://www.cnblogs.com/peida/archive/2013/01/08/2850483.html)
