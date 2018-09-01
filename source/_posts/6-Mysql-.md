---
title: mysql相关的一些总结
comments: true
date: 2018-4-4 20:17
categories: [mysql]
tags: [mysql]
keywords: mysql
---

## 四种在MySQL中修改root密码的方法
### 方法1： 用SET PASSWORD命令
```
  $ mysql -u root
  mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');
```
### 方法2：用mysqladmin
```
  $ mysqladmin -u root password "newpass"

  # 如果root已经设置过密码，采用如下方法
  mysqladmin -u root password oldpass "newpass"
```
### 方法3： 用UPDATE直接编辑user表
```
  $ mysql -u root

  mysql> use mysql;

  mysql> UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';

  mysql> FLUSH PRIVILEGES;
```
### 方法4：在丢失root密码的时候，可以这样
```
  # 跳过权限认证
  $ mysqld_safe --skip-grant-tables

  $ mysql -u root mysql

  mysql> UPDATE user SET password=PASSWORD("newpass") WHERE user='root';
  # 刷新权限
  mysql> FLUSH PRIVILEGES;
```


## 根据条件导出部分数据
```
# 其中 --where 用来指定查询的条件
mysqldump -u root -p --no-create-db=TRUE --add-drop-table=FALSE --where="user_id >= 1000" 数据库名 表名 --skip-lock-tables > data.sql
```

