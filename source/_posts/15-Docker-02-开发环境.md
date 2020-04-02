---
title: Docker安装开发环境-[redis,zookeeeper等]
comments: true
date: 2020-03-31 17:36
categories: [Docker]
tags: [Docker]
---

## 查看可用镜像
访问： <https://hub.docker.com/_/redis?tab=tags>
or
`docker search redis`

## 安装redis
```shell
docker pull redis
# 查看latest的具体版本
docker inspect redis:latest | grep -i version

# 最简单的启动方式
# docker run --name='redis' -itd -p 6379:6379 redis

# 挂载宿主机文件启动
docker run --name='redis' -itd \
-v /opt/docker/redis/conf/redis.conf:/usr/local/etc/redis/redis.conf \
-v /opt/docker/redis/data:/var/lib/redis \
-v /opt/docker/redis/log:/var/log/redis \
-p 6379:6379 \
redis \
/usr/local/bin/redis-server /usr/local/etc/redis/redis.conf --appendonly yes
```

<!-- more -->
## 安装tomcat
```sh
docker pull tomcat

# 运行，宿主机使用80端口
docker run --name tomcat -itd \
-v /opt/docker/tomcat/webapps:/usr/local/tomcat/webapps \
-v /opt/docker/tomcat/log:/usr/local/tomcat/logs \
-v /opt/docker/tomcat/work:/usr/local/tomcat/work \
-v /opt/docker/tomcat/temp:/usr/local/tomcat/temp \
-p 80:8080 \
tomcat


# 接下来在本地的 /opt/docker/tomcat/webapps 目录可以放war包
- springboot war包？
```

