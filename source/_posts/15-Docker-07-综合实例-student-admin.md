---
title: Docker-Compose部署jar包
comments: true
date: 2020-09-31 23:36
categories: [Docker]
tags: [Docker]
---

## 目录结构准备

.
|-- docker-compose.yml
`-- student-admin
    |-- Dockerfile
    `-- student-admin-1.0-SNAPSHOT.jar

## docker配置编写

docker-compose.yml
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

student-admin/Dockerfile 
```Dockerfile
FROM java:8
ADD *.jar app.jar
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-jar","/app.jar","--spring.config.location=/data/javaapps/config/application-test.properties"]
```


`student-admin-1.0-SNAPSHOT.jar` SpringBoot项目打的jar包
运行时通过 `--spring.config.location` 指定外部配置文件


## springboot配置文件修改

/data/javaapps/config/application-test.properties
```yml
server.port=8001

db.url=jdbc:mysql://mysql:3306/student_admin?characterEncoding=UTF-8
db.username=test
db.password=test

mybatis.config-location=classpath:mybatis-config.xml
```

**注: 这里mysql直接使用另一个docker容器中启动的mysql,它们处于同一个网络app_net**


## 测试

访问 `http://localhost:8001/api/getAllStudent`