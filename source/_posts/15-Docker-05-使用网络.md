---
title: Docker使用网络
comments: true
date: 2020-09-31 21:36
categories: [Docker]
tags: [Docker]
---

## 使用网络

```sh
docker network create -d bridge my-net
```

-d 参数指定 Docker 网络类型，有 bridge overlay。其中 overlay 网络类型用于 Swarm mode

更多的容器网络互连， 使用 docker-compose


## docker-compose 网络

1. 创建网络 
`docker network create app_net`

查看当前网络：`docker network ls`

2. nginx-test1 环境

test1/docker-compose.yml
```yaml
version: "3"
services:
  nginx-test1:
    image: nginx
    container_name: nginx-test1
    command: /bin/bash -c "echo 'test11'>/usr/share/nginx/html/index.html && exec nginx -g 'daemon off;'"
    networks:
      - default
      - app_net
networks:
  app_net:
    external: true
```

2. nginx-test2 环境

test2/docker-compose.yml
```yaml
version: "3"
services:
  nginx-test2:
    image: nginx
    container_name: nginx-test2
    command: /bin/bash -c "echo 'test22'>/usr/share/nginx/html/index.html && exec nginx -g 'daemon off;'"
    networks:
      - default
      - app_net
networks:
  app_net:
    external: true
```

3. 分别通过`docker-coompose up -d`启动两个容器

4. 通过`curl`测试网络连接 

分别测试：
```sh
docker exec -it nginx-test1 curl nginx-test2
# output: test22


docker exec -it nginx-test2 curl nginx-test1
# output: test11

```

可以看到修改后的html输出，证明网络联通正常


## 参考
> <https://yeasy.gitbook.io/docker_practice/data_management/volume>
