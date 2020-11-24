---
title: 折腾NAS之旅-docker-compose
comments: true
date: 2020-10-25 13:59
categories: [NAS]
tags: [NAS]
---

## 目的
使用docker 部署一个nginx,用于通过docker的网络访问其他docker容器,这样宿主机只需要暴露nginx的端口给外部，其他docker容器都通过nginx内部网络转发，不需要再暴露端口给外部，方便统一管理对外端口，大大增加安全性。


## 安装 docker-compose

```sh
curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

docker-compose --version
```

## 创建网络 
`docker network create app_net`

## 准备docker-compose.yml 文件

```yml
version: "3"
services:
  student-admin:
    image: stan/student-admin:1.1
    build: ./student-admin
    container_name: student-admin
    volumes:
     - /data/javaapps/config:/data/javaapps/config 
    ports:
     - "8001:8001"
    networks:
      - app_net

networks:
  app_net:
    external: true
```