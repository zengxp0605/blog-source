---
title: Docker数据管理
comments: true
date: 2020-09-31 20:36
categories: [Docker]
tags: [Docker]
---

## 使用数据卷 Volume

创建数据卷
```sh
docker volume create nginx-html
```

```sh
# 列出数据卷
docker volume ls

# 查看数据卷详情
docker volume inspect nginx-html
```

启动容器时挂载数据卷
```sh
docker run -d --name mynginx \
#-v nginx-html:/usr/share/nginx/html \
--mount source=nginx-html,target=/usr/share/nginx/html \
-p 8380:8380  mynginx
```
**注：如果数据卷nginx-html为空，/usr/share/nginx/html中的文件会自动复制过来；不为空则不会**


删除数据卷
```
docker volume rm nginx-html
```

清理无主数据卷
`docker volume prune`



## 挂载主机目录

```sh
docker run -d --name mynginx \
-v /opt/nginx/html:/usr/share/nginx/html \
-p 8380:8380  mynginx
```

`-v` 使用绝对路径



## 参考
> <https://yeasy.gitbook.io/docker_practice/data_management/volume>
