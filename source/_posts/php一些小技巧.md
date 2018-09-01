---
title: php一些小技巧
comments: true
date: 2015-03-15 22:33:57
categories: [php]
tags: [php]
keywords:
---

## php cli命令行运行,kill 自身

```
    pid = getmypid(); // 获取pid
    exec("ps -eo%mem,rss,pid | grep $pid", $output); // 可以得到内存占用比例。
    exec("kill $pid"); // 设置一个阀值，大于某个值后执行;
```

