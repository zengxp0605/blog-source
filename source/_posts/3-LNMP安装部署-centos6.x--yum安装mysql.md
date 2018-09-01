---
title: CentOS6.8 通过yum安装mysql5.6
comments: true
date: 2017-8-11 14:21
categories: [LNMP]
tags: [LNMP]
keywords: yum install mysql
---
# 通过更新mysql的yum源,安装mysql5.6

> 安装前要检查机器原来是否安装过mysql，如有安装需要先进行数据备份、清理

- 检查环境

```bash
 yum list installed | grep mysql
 ps -ef|grep mysql
 service mysqld stop 
 rpm -e mysql-libs --nodeps
 yum -y remove mysql mysql-*
```

- 设置安装源

```bash
wget http://repo.mysql.com/mysql57-community-release-el6-8.noarch.rpm
rpm -ivh mysql57-community-release-el6-8.noarch.rpm
ls -1 /etc/yum.repos.d/mysql-community*
yum repolist all | grep mysql
vi /etc/yum.repos.d/mysql-community.repo
### 将[mysql56-community]的enabled设置为1,[mysql57-community]的enabled设置为0 ### 
yum repolist enabled | grep mysql

```

> 注意: 如果不使用5.7 或者其他任何版本，只能有一个是 enabled=1的，其他的都得enabled=0。

- 安装mysql

```bash
yum -y install mysql-server mysql
```

<!--more-->

- 修改默认配置(见最后的推荐配置)

- 启动服务

```bash
service mysqld start
```

- 修改root密码(默认root密码为空)

```bash
$ mysql -u root
mysql> use mysql;
mysql> update user set password=PASSWORD("GIVE-NEW-ROOT-PASSWORD") where User='root';
mysql> flush privileges;
mysql> quit

```

## 推荐的一些配置

```config
[client]  
 port=3306 
 socket=/tmp/mysql.sock 
  　　
 [mysql] 
 default-character-set=utf8 
  　　
 [mysqld] 
 user=mysql 
 character-set-server=utf8 
 explicit_defaults_for_timestamp=true 

 #索引和数据缓冲区大小，一般设置物理内存的60%-70%   　　
 innodb_buffer_pool_size = 1G 
 #缓冲池实例个数，推荐设置4个或8个 
 innodb_buffer_pool_instances = 4 
 #关键参数，0代表大约每秒写入到日志并同步到磁盘，数据库故障会丢失1秒左右事务数据。1为每执行一条SQL后写入到日志并同步到磁盘，I/                                                             O开销大，执行完SQL要等待日志读写，效率低。2代表只把日志写入到系统缓存区，再每秒同步到磁盘，效率很高，如果服务器故障，才会丢失事务数据。对数据安全性要求不是很高的推荐设置2，性能高，修改后效果明显。
 innodb_flush_log_at_trx_commit = 2   
 #默认是共享表空间，共享表空间idbdata文件不断增大，影响一定的I/O性能。推荐开启独立表空间模式，每个表的索引和数据都存在自己独立的表空间中，可以实现单表在不同数据库中移动。 
 innodb_file_per_table = ON 
 #日志缓冲区大小，由于日志最长每秒钟刷新一次，所以一般不用超过16M 
 innodb_log_buffer_size = 8M    
    
 datadir=/data/mysql 
 socket=/tmp/mysql.sock 

 # 禁止MySQL对外部 连接进行DNS解析
 skip-name-resolve    
 # Disabling symbolic-links is recommended to prevent assorted security risks 
 symbolic-links=0 
    
 # Recommended in standard MySQL setup 
 sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES  
    
 [mysqld_safe] 
 log-error=/var/log/mysqld.log 
 pid-file=/var/run/mysqld/mysqld.pid

```


> 转载自: [https://www.jianshu.com/p/5a2e4e0b6eda](https://www.jianshu.com/p/5a2e4e0b6eda)
