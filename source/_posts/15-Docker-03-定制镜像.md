---
title: Docker定制镜像
comments: true
date: 2020-09-31 17:36
categories: [Docker]
tags: [Docker]
---

## 使用 Dockerfile 定制镜像

```sh
mkdir mynginx
cd mynginx/
vi Dockerfile
```

Dockerfile 内容如下：
```
FROM nginx
RUN echo '<h1>Hello, Docker! mynginx</h1>' > /usr/share/nginx/html/index.html \
    && echo 'test1' > /usr/share/nginx/html/test.html
```

## 构建镜像

```sh
docker build -t nginx:v3 .
```

**注：最后的`.`表示的是Docker引擎服务端的上下文路径**


构建成功后可以通过 `docker image ls` 查看新的镜像

通过`docker history nginx:v3` 可以查看镜像历史信息


## 启动容器

```sh
docker run -d --name mynginx -p 8280:80 nginx:v3
```

启动后可以访问`http://localhost:8280/` `http://localhost:8280/test.html` 查看到对应的页面 


## Dockerfile 其他指令

### COPY <源路径> <目标路径>
> ADD 指令也可以复制文件，并且会自动解压 tar.gz 等压缩文件

使用 COPY指令复制nginx配置文件

自定义端口的 `default.conf`
```
server {
    listen       8380;
    listen  [::]:8380;
    server_name  localhost;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```

Dockerfile 
```
FROM nginx
COPY --chown=root:root ./files/default.conf /etc/nginx/conf.d/
RUN echo '<h1>Hello, Docker! mynginx</h1>' > /usr/share/nginx/html/index.html \
    && echo 'test1' > /usr/share/nginx/html/test.html
    
EXPOSE 8380    
```

构建&&运行
```sh
docker build -t mynginx:v4 .
docker run -d --name mynginx -p 8380:8380 mynginx:v4
```

### CMD 前台和后台执行

使用 `CMD service nginx start`命令
会被理解为 CMD [ "sh", "-c", "service nginx start"]，因此主进程实际上是 sh。那么当 service nginx start 命令结束后，sh 也就结束了，sh 作为主进程退出了，自然就会令容器退出。
正确的做法是直接执行 nginx 可执行文件，并且要求以前台形式运行。比如：
`CMD ["nginx", "-g", "daemon off;"]`

### ENTRYPOINT 入口点

当指定了 ENTRYPOINT 后，CMD 的含义就发生了改变，不再是直接的运行其命令，而是将 CMD 的内容作为参数传给 ENTRYPOINT 指令，换句话说实际执行时，将变为： `<ENTRYPOINT> "<CMD>"`

### EXPOSE 

EXPOSE 指令是声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务



## 参考
> <https://yeasy.gitbook.io/docker_practice/image/build>
