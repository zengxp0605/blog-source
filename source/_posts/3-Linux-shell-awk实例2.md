---
title: Linux shell命令-awk实例2
comments: true
date: 2017-12-6 21:07
categories: [Linux]
tags: [Linux]
keywords: Linux命令
---

## 统计日志文件中耗时数据
- 如下日志文件`tmp.log`
```log
[2017-12-11 10:27:52.745] [INFO] act耗时: 26 ms
[2017-12-11 10:27:52.752] [INFO] act耗时: 355 ms
[2017-12-11 10:27:52.752] [INFO] act耗时: 135 ms
[2017-12-11 10:27:52.753] [INFO] act耗时: 532 ms
[2017-12-11 10:27:52.753] [INFO] act耗时: 632 ms
[2017-12-11 10:27:52.753] [INFO] act耗时: 92 ms
[2017-12-11 10:27:52.753] [INFO] act耗时: 1092 ms
[2017-12-11 10:27:52.753] [INFO] act耗时: 2012 ms
[2017-12-11 10:27:52.753] [INFO] act耗时: 9 ms
```

- 需要统计<100ms;100~499ms;500~999ms 等各个时段的数量

- 使用`awk`命令处理
```bash
awk -F ": " '/act耗时/ {
	num=int(substr($2,1,length($2)-3));
	if(num < 100) c100++
	else if(num >= 100 && num < 500) c4_9++
	else if(num >= 500 && num < 1000) c5_9++
	else if(num >= 1000 && num < 2000) c1000++
	else if(num >= 2000 ) c2000++
}END{
  print "<100ms: " c100 \
	"\n100~499ms: " c4_9 \
	"\n500~999ms: " c5_9 \
	"\n1000~1999ms: " c1000 \
	"\n>2000ms: " c2000
}' tmp.log

```

- 得到输出结果
```
<100ms: 3
100~499ms: 2
500~999ms: 2
1000~1999ms: 1
>2000ms: 1
```

### 使用`time`命令可以查看运行时间
- 例如:
```
time awk -F ": " '/act耗时/ {print $2}' tmp.log
```

- 得到结果
```
real	0m0.004s
user	0m0.000s
sys	    0m0.003s
```
