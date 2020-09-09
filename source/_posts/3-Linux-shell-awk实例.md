---
title: Linux shell命令-awk实例
comments: true
date: 2017-12-6 20:07
categories: [Linux]
tags: [Linux]
keywords: Linux命令
---

## 获取文件中最大的端口号

### 如下nginx配置文件,需要获取到`proxy_pass`后面端口号的最大值

```conf
server {
    listen 9001;
    server_name localhost;
    location / {
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-Forwarded-For $http_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://127.0.0.1:5023;
    }
}

server {
    listen 9002;
    server_name localhost;
    location / {
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-Forwarded-For $http_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://127.0.0.1:5013;
    }
}

server {
    listen 9003;
    server_name localhost;
    location / {
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-Forwarded-For $http_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://127.0.0.1:6003;
    }
}

server {
    listen 9004;
    server_name localhost;
    location / {
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-Forwarded-For $http_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://127.0.0.1:5043;
    }
}
```

### shell中使用命令
- 注意使用单引号
```bash
N=`awk -F ':' '/proxy_pass/{print $3}' /usr/local/nginx/conf/vhosts/host.conf | awk -F ';' '{print $1}' | sort -r | head -n1`
echo $N
```

### 命令分析
- `awk -F ':' '/proxy_pass/{print $3}' host.conf`
  - 得到包含'proxy_pass'的行,打印使用':'分割的第三列
  - 结果为
    ```
        5023;
        5013;
        6003;
        5043;
    ```
- `awk -F ';' '{print $1}'`    
  - 使用';' 分割获取的结果,打印第一列
  - 结果为
    ```
        5023
        5013
        6003
        5043
    ```
- `sort -r` 使结果降序排列
- `head -n1` 从头部获取第一行
  - 最后得到结果: `6003`


### 思考: 如果同时要获取到夹杂着如下格式的端口怎么处理?
```
upstream test1 {
    server 127.0.0.1:7001;
}

upstream test2 {
    server 127.0.0.1:7021;
}
```

- 解决方案
  - 第一个命令可以改为 `awk -F ':' '{print index($0,"server") ? $2 : $3 }'`
  - `$0` 这个变量包含执行过程中当前行的文本内容。
  - `index()`函数 可以判断字符串是否包含 'server'这个字符 (或者使用`match`函数,利用正则)
  - 然后通过条件判断语句输出不同的列


### 扩展
- 传递外部参数
```bash
currentBranch="master"; echo 1111 | awk '{print currentBranch}' currentBranch=$currentBranch
```

- 使用`if`条件语句
```bash
$ awk '{if($6 > 50) {count++;print $3;} else {x+5;print $2;}}' filename

$ awk {
  if($3 > 89 && $3 <101) Agrade++
  else if($3 > 79) Bgrade++
  else if($3 > 69) Cgrade++
  else if($3 > 59) Dgrade++
  else Fgrade++
}END{
  print "The number of failures is "Fgrade
} filename

```

- 行匹配: 匹配用~,不匹配用!~。
```bash
// 例1 行匹配：匹配行内包含 ls 的行文本数据
$ awk '$0~/ls/ {print}' filename   或者  awk '/ls/ {print}' filename 

```


> 参考: [http://man.linuxde.net/awk](http://man.linuxde.net/awk)
> [http://www.gnu.org/software/gawk/manual/gawk.html](http://www.gnu.org/software/gawk/manual/gawk.html)
