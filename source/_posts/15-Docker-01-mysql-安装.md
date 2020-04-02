---
title: Docker安装mysql
comments: true
date: 2020-03-30 17:36
categories: [Docker]
tags: [Docker]
---

## 查看可用的 MySQL 版本
访问 MySQL 镜像库地址：https://hub.docker.com/_/mysql?tab=tags 

## 拉取 MySQL 镜像
默认是最新版本 mysql:latest 
```
docker pull mysql
```

## 运行容器
```
docker run -itd -p 3306:3306 \
-e MYSQL_USER="test" \
-e MYSQL_PASSWORD="a123456" \
-e MYSQL_ROOT_PASSWORD="a123456" \
--name mysqtest \
mysql \
--character-set-server=utf8 \
--collation-server=utf8_general_ci \
--default-authentication-plugin=mysql_native_password
```

- 参数说明
```
-itd：i是交互式操作，t是一个终端，d指的是在后台运行
-e 环境变量使用的方式设置在镜像名称的前面
–character-set-server=utf8：设置字符集为utf8
–collation-server=utf8_general_ci：设置字符比较规则为utf8_general_ci
```

## 进入容器设置权限
接下来我们使用exec命令进入一个已经在运行的容器
```sh
# 查看容器id
docker ps  -a
docker exec -it 465168ee9c69 /bin/bash  
```

设置权限给test用户
```sh
grant all privileges on *.* to test@"%";
flush privileges;
```

## 数据和配置挂载到宿主机
- 先删除刚才启动的容器
```
# 停止
docker stop 465168ee9c69
# 删除
docker rm 465168ee9c69
```

- 为了安全性，我们应该将数据和配置放到宿主机中,首先创建目录

```
sudo mkdir /opt/docker
sudo chown -R stan:staff /opt/docker
mkdir mysql
cd mysql
mkdir data config
cd config
touch my.cnf
```

- my.cnf配置文件内容
```
[mysqld]
user=mysql
character-set-server=utf8
default_authentication_plugin=mysql_native_password

[client]
default-character-set=utf8

[mysql]
default-character-set=utf8
```

- 执行下面的命令运行
```sh
docker run -itd -p 3306:3306 \
--privileged=true \
-v /opt/docker/mysql/config/my.cnf:/etc/my.cnf \
-v /opt/docker/mysql/data:/var/lib/mysql \
-e MYSQL_USER="test" \
-e MYSQL_PASSWORD="a123456" \
-e MYSQL_ROOT_PASSWORD="a123456" \
--name mysql \
mysql \
--character-set-server=utf8 \
--collation-server=utf8_general_ci \
--default-authentication-plugin=mysql_native_password 
```

参数说明
```
–privileged=true：提升容器内权限
-v /mysql/config/my.cnf:/etc/my.cnf：映射配置文件
-v /mysql/data:/var/lib/mysql：映射数据目录
```


## docker 扩展
- 停止所有的container（容器），这样才能够删除其中的images：
```
docker stop $(docker ps -aq) 
```

- 删除所有container（容器）
```
docker rm $(docker ps -aq) 
```

- 查看当前images
```
docker images
```

- 删除images（镜像），通过image的id来指定删除谁

```
docker rmi <image id>
```

- 删除全部image（镜像）加 -f 强制删除
```
docker rmi $(docker images -q)
```
