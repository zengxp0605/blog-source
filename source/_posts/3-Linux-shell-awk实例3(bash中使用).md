---
title: Linux shell命令-awk实例-统计日志数据
comments: true
date: 2018-3-27 09:52
categories: [Linux]
tags: [Linux]
keywords: Linux命令
---

### 输出日志格式
```js
# 日志关键字 ready TotalTime
app.logger.info('ready TotalTime:' + (Date.now() - startTime));

# 日志格式如下:
[2018-03-05 09:47:11.463] [INFO] syslog - [INFO] => ready TotalTime:59
[2018-03-05 09:47:11.464] [INFO] syslog - [INFO] => take TotalTime:200
[2018-03-05 09:47:11.474] [INFO] syslog - [INFO] => take TotalTime:21
[2018-03-05 09:47:11.484] [INFO] syslog - [INFO] => pass TotalTime:144

```

### 合并多个日志文件
```sh
# 新建或清空文件
echo "" > time.log

# 提取关键字到指定文件
grep TotalTime syslog-2018-03-03.log >> time.log
grep TotalTime syslog-2018-03-04.log >> time.log
grep TotalTime syslog-2018-03-05.log >> time.log
```

### 运行`yace.sh`
- 添加执行权限 `chmod 777 yace.sh`
- 脚本文件与日志文件同一目录
- 文件见末尾


### 统计结果说明(结果位于`res.log`文件)
```
==========pass==========
<100ms: 2   # 表示 <100ms 的数量为2
100ms:  2   # 表示 >=100ms   <2000ms
900ms:  1   # 表示 >=900ms   <1000ms
1200ms: 1   # 表示 >=1200ms   <1300ms
```

### 基础统计命令
```
awk -F ":" '/TotalTime/ {num=$4;idx=int(num/100);array[idx]+=1;}END{
  print "----------";
  for(i in array){
    if(i==0) print "<100ms:\t" array[i];
    else  print i"00ms:\t" array[i];
  }
  print "----------";
}' time.log
```

### 统计脚本(多命令批量处理)
```yace.sh

#/bin/bash
inputFile="time.log"  # 输入日志
outputFile="res.log"  # 输出的结果

# 需要统计的命令
cmdArr=("take" "pass" "ready");

echo "" > $outputFile
echo "统计开始:"`date "+%Y-%m-%d %H:%M:%S"`>> $outputFile

for val in ${cmdArr[@]};
do
  echo "正在统计 ${val} 命令"
  echo -e "\n\n=========="$val"==========" >> $outputFile;
  awk -F ":" '/'$val' TotalTime/ {num=$4;idx=int(num/100);array[idx]+=1;}END{
  for(i in array){
    if(i==0) print "<100ms:\t" array[i];
    else  print i"00ms:\t" array[i];
  }
  array[i]=0;
}' $inputFile >> $outputFile;
done

echo -e "\n\n统计结束:"`date "+%Y-%m-%d %H:%M:%S"`>> $outputFile
echo "统计结束,查看结果: cat ${outputFile}"

```
