---
title: 使用top+awk命令,记录cpu占用情况,并统计平均值
comments: true
date: 2018-12-20 10:46
categories: [Linux]
tags: [Linux]
keywords: Linux命令
---

## 记录cpu占用数据
```bash
echo -n `top -n 10 -d 1 | grep node | awk -F ' ' '{print $10}'` >> tmp.log 
```

说明:
- `echo`的参数`-n`及后面的 `>>` 表示追加字符到`tmp.log`时不换行(节省文件大小))
- `top`的参数`-n`表示输出top结果的次数, `-d`表示指定每两次屏幕信息刷新之间的时间间隔
- `grep node` 表示筛选node进程, (这里只启用了一个node进程,如果有多个node进程,可以`top`时使用`-p`指定进程的pid)
- `awk` `$10`正好是`cpu`的占用数据


## 统计数据
```bash
awk -F ' ' '{total=0;i=1; while (i<=NF) {total=total+$i; i++}}END{printf "total: %s,count: %s,average: %s",total,NF,total/NF}' tmp.log
```

## awk详解
1. 工作原理：

awk 会把每行进行一个拆分，用相应的命令对拆分出来的“段”进行处理。

（1）行工作模式，读入文件的每一行，会把一行的内容，存到$0里

（2）使用内置的变量FS(段的分隔符，默认用的是空白字符)，分割这一行，把分割出来的每个段存到相应的变量$(1-100)

（3）输出的时候按照内置变量OFS(out FS)，输出

（4）读入下一行继续操作

简单实例

[root@tx3 ~]# echo "this is a book" > awk.txt

[root@tx3 ~]# awk '{print $2,$1,$3,$4}' awk.txt

is this a book



2. Awk常用内置变量表：

1 $0             当前记录（作为单个变量）  

2 $1~$n          当前记录的第n个字段，字段间由FS分隔  

3 FS             输入字段分隔符 默认是空格  

4 NF             当前记录中的字段个数，就是有多少列  

5 NR             已经读出的记录数，就是行号，从1开始  

6 RS             输入的记录他隔符默 认为换行符  

7 OFS            输出字段分隔符 默认也是空格  

8 ORS            输出的记录分隔符，默认为换行符  

9 ARGC           命令行参数个数  

10 ARGV           命令行参数数组  

11 FILENAME       当前输入文件的名字  

12 IGNORECASE     如果为真，则进行忽略大小写的匹配  

13 ARGIND         当前被处理文件的ARGV标志符  

14 CONVFMT        数字转换格式 %.6g  

15 ENVIRON        UNIX环境变量  

16 ERRNO          UNIX系统错误消息  

17 FIELDWIDTHS    输入字段宽度的空白分隔字符串  

18 FNR            当前记录数  

19 OFMT           数字的输出格式 %.6g  

20 RSTART         被匹配函数匹配的字符串首  

21 RLENGTH        被匹配函数匹配的字符串长度  


## 其他示例
- 通过`getline`从管道和标准输入读取输入，然后传递给变量
```bash 
echo str | awk 'BEGIN{"date"| getline a}{print}END{print a}'
```

- 保存cpu的数据不换行(方便excel中生成图表)
```bash
top -n 3600 -d 1 | grep node | awk -F ' ' '{print $10}' >> tmp.log 
awk 'BEGIN{total=0;count=0;}{if($0 != ""){total=total+$0; count++}}END{printf "total: %s,count: %s,argv: %s",total,count,total/count}' tmp.log
```

- 结合`sort`, `uniq` 获取唯一的id
```
cat syslog-2019-02-26.log | grep 系统异常 |  awk -F "{" '{print $4}' | awk -F ':' '{print $2}' | awk -F ',' '{print $1}' | sort | uniq | wc -l
```

## 参考
- [Shell脚本之awk详解](http://blog.51cto.com/tanxin/1222140)
