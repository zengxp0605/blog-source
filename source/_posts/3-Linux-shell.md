---
title: Linux shell命令
comments: true
date: 2016-5-11 15:07
categories: [Linux]
tags: [Linux]
keywords: Linux命令
---

### cat 输出文件内容

> 功能：把一个或者多个文件（或者标准输入）连接在一起，并标准输出。（Concatenate FILE(s), or standard input, to standard output.）  
> cat命令常用来显示文件内容，或者将几个文件连接起来显示，或者从标准输入读取内容并显示。它常与重定向符号配合使用。cat是Concatenate的缩写。  


- 语法：cat   [选项]   [文件]  

```
短选项 长选项 涵义
-A  --show-all  等于-vET
-b  --number-nonblank   对非空输出行编号
-e      等于-vE
-E  --show-ends 在每行结束处显示"$"
-n  --number    对输出的所有行编号
-s  --squeeze-blank 不输出多行空行
-t      与-vT 等价
-T  --show-tabs 将跳格字符显示为^I
-v  --show-nonprinting  使用^ 和M- 引用，除了LFD和 TAB 之外

```


### head 显示文件开头若干行内容

>  功能：head命令显示文件开头若干行（默认10行）。  
>  将每个指定文件的头10 行显示到标准输出。如果指定了多于一个文件，在每一段输出前会给出文件名作为文件头。如果不指定文件，或者文件为"-"，则从标准输入读取数据。  

- 语法：head   [选项]   [文件]

```
短选项 长选项 涵义
-c [-]K --bytes=[-]K    每个文件显示开头K字节。若使用了-K，则显示文件全部内容，除去最后K字节
-n [-]K --lines=[-]K    每个文件显示开头K行。若使用了-K，则显示文件全部内容，除去最后K行
-q  --quiet 或 --silent  不显示包含给定文件名的文件头
-v  --verbose   总是显示包含给定文件名的文件头（默认）

```


### tail 显示文件最后若干行内容

>  功能：tail命令可以输出文件的尾部内容，默认情况下它显示文件的最后十行。
>  显示每个指定文件的最后10 行到标准输出。若指定了多于一个文件，程序会在每段输出的开始添加相应文件名作为头。如果不指定文件或文件为"-" ，则从标准输入读取数据。
>  它常用来动态监视文件的尾部内容的增长情况，比如用来监视日志文件的变化。


- 语法：tail   [选项]   [文件]

```
短选项 长选项 涵义
-c [+] K    --bytes=[+] K   输出最后 K 字节；另外，使用-c +K 从每个文件的第 K 字节输出
-n [+] K    --lines=[+] K   输出最后 K 行，代替最后10 行；使用-n +K 从每个文件的第 K 字节输出
-f  --follow=descriptor
--follow=name   descriptor是 --follow 默认值，所以 -f 等价 --follow 等价--follow=descriptor
即时输出文件变化后追加的数据。
tail -f file 动态跟踪文件file的增长情况，tail会每隔一秒去检查一下文件是否增加新的内容。如果增加就追加在原来的输出后面显示。但这种情况，必须保证在执行tail命令时，文件已经存在。
如果想终止 tail -f 输出，按 Ctrl+C 中断tail程序。如果按Ctrl+C不能中断输出，那么可以在别的终端上执行 killall tail 强行终止。
    --pid=PID   同 -f 使用，当 PID 所对应的进程死去后，终止。
    ---retry    即使目标文件不可访问依然试图打开；在与参数-f 或 --follow=name 同时使用时常常有用。
-F  --follow=name --retry   与 -f 相同，也是动态跟踪文件的变化，不同的是执行此命令时文件可以不存在。
    --max-unchanged-stats=N N 默认为5，使用--follow=name，重新打开一个在 N 次迭代后没有改变大小的文件来看它是否被解除连接或重命名（这是循环日志文件的通常情况）。
由于有inotify，这个选项很少使用。
-s S    --sleep-interval=S  与 -f 合用,表示在每次反复的间隔休眠 S 秒
对于 --pid=PID ，每隔 S 秒检查进程。
-q  --quiet 或 --silent  不输出文件名
-v  --verbose   总是输出给出文件名的首部

```

### wc 统计文件行数

> 说明：该命令统计给定文件中的字节数、字数、行数。如果没有给出文件名，则从标准输入读取。wc同时也给出所有指定文件的总统计数。字是由空格字符区分开的最大字符串。

- 语法：wc  [选项]  [文件]

该命令各选项含义如下：
```
　　- c 统计字节数。

　　- l 统计行数。

　　- w 统计字数。
```

### date 时间日期
- 以指定格式显示文件更改后最后日期，如yyyy-mm-dd hh24:mi:ss 
```
$ date "+%Y-%m-%d %H:%M:%S" -r test.bak  
```
- 显示结果如: `2017-12-07 18:06:55`
- shell 中赋值
```
DATE=$(date +%Y%m%d)
DATE=`date +%Y%m%d`
date1=$(date --date='1 days ago +%Y%m%d')    #前一天的日期
date2=$(date --date='2 days ago +%Y%m%d')    #前l两天的日期
```

### sort 排序
- 语法：sort [-fbMnrtuk] [file or stdin]
- 选项与参数：
```
-f  ：忽略大小写的差异，例如 A 与 a 视为编码相同；
-b  ：忽略最前面的空格符部分；
-M  ：以月份的名字来排序，例如 JAN, DEC 等等的排序方法；
-n  ：使用『纯数字』进行排序(默认是以文字型态来排序的)；
-r  ：反向排序；
-u  ：就是 uniq ，相同的数据中，仅出现一行代表；
-t  ：分隔符，默认是用 [tab] 键来分隔；
-k  ：以那个区间 (field) 来进行排序的意思
```

如通过耗时日志排序
每行log格式： `2019-01-02 09:28:31.521 INFO xxxxxHandler耗时,ms=123`
```
cat info.log | grep "耗时" ｜ sort -t "=" -k 3 -n
```

### uniq
> 重复行必须是相邻的，所以一般需要先sort 排序
- 语法：uniq [-icu]
- 选项与参数：
```
-i   ：忽略大小写字符的不同；
-c  ：进行计数
-u  ：只显示唯一的行
```

如统计log日期次数  
每行log格式： `2019-01-02 09:28:31.521 DEBUG xxxxxGenerator: index`
```
cat logs/* | grep xxx | awk '{print $1}' | sort | uniq -c
```

### sed 某个指定时间段的内容
```sh
sed -n '/起始时间/,/结束时间/p' 日志文件

例如，sed -n '/2014-12-17 16:17:20/,/2014-12-17 16:17:36/p'  test.log

```

### 其他常用命令
1. 查看端口占用: `netstat -tplne`

2. 查看端口是否占有: `lsof -i:8080`

3. 查看文件夹占用空间 du(disk usage)
	- `du -sh`  # 查看当前目录总共占的容量。而不单独列出各子项占用的容量
	- `du -lh --max-depth=1`  # 查看当前目录下一级子文件和子目录占用的磁盘容量。
	
4. 查看设备的空间使用率: `df -lh`


