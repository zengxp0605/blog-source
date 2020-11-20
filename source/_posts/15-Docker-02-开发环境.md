---
title: Docker安装开发环境-redis,zookeeeper等
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
--cap-add=SYS_PTRACE \
-v /opt/docker/tomcat/webapps:/usr/local/tomcat/webapps \
-v /opt/docker/tomcat/log:/usr/local/tomcat/logs \
-v /opt/docker/tomcat/work:/usr/local/tomcat/work \
-v /opt/docker/tomcat/temp:/usr/local/tomcat/temp \
-p 80:8080 \
tomcat

# 接下来在本地的 /opt/docker/tomcat/webapps 目录可以放war包
```

参数说明：
```
--cap-add=SYS_PTRACE # Docker 自 1.10 版本开始，默认的 seccomp 配置文件中禁用了 ptrace，容器中无法使用jmap等命令
# 会报错如下：
Error attaching to process: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process: ptrace(PTRACE_ATTACH, ..) failed for 123: Operation not permitted
```


## 安装gitlab 
> 需要最低4G内存。

docker run --detach \
--publish 22443:443 --publish 8180:80 --publish 2222:22 \
--memory 4g \
--name gitlab \
--restart always \
--volume /home/docker/gitlab/config:/etc/gitlab \
--volume /home/docker/gitlab/logs:/var/log/gitlab \
--volume /home/docker/gitlab/data:/var/opt/gitlab \
gitlab/gitlab-ce:latest


## 安装nginx [废弃-容器内端口转发有问题？]

- 先启动一个nginx
```sh
  docker run -d \
  --name nginx \
  -p 80:80 \
  nginx
```

- 复制配置文件到需要挂载的目录
```
docker cp nginx:/etc/nginx/* /opt/docker/nginx/conf
docker cp nginx:/usr/share/nginx/html /opt/docker/nginx
```

- 删除之前的nginx容器，再启动一个使用本地配置的nginx
```sh
docker run -d \
  --name nginx \
  --volume /opt/docker/nginx/html:/usr/share/nginx/html \
  --volume /opt/docker/nginx/conf:/etc/nginx \
  --net=host \
  nginx 

```
