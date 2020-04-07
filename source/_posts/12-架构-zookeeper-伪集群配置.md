---
title: zookeeper伪集群配置
comments: true
date: 2019-10-9 10:43
categories: [zookeeper]
tags: [zookeeper]
keywords: zookeeper
---

## 一台机器上启动多个zookeeper实例
> 环境: 阿里云 CentOS release 6.10

1. 下载 `zookeeper` 
- 需要下载带 `-bin` 的版本,否则启动时会报错
- `wget https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/stable/apache-zookeeper-3.5.5-bin.tar.gz`
- 解压 `tar zxvf apache-zookeeper-3.5.5-bin.tar.gz`

2. 拷贝3份解压后的目录 
- mkdir /usr/local/zookeeper
- cp -rf apache-zookeeper-3.5.5-bin /usr/local/zookeeper/zoo1
- cp -rf apache-zookeeper-3.5.5-bin /usr/local/zookeeper/zoo2
- cp -rf apache-zookeeper-3.5.5-bin /usr/local/zookeeper/zoo3

<!-- more -->

3. 修改配置
- cp zoo1/conf/zoo_sample.cfg zoo1/conf/zoo.cfg
- 编辑`zoo1/conf/zoo.cfg`
```
dataDir=/usr/local/zookeeper/zoo1/data
clientPort=2181
server.1=127.0.0.1:2887:3887
server.2=127.0.0.1:2888:3888
server.3=127.0.0.1:2889:3889
```
- 编辑`zoo2/conf/zoo.cfg`
```
dataDir=/usr/local/zookeeper/zoo2/data
clientPort=2182
server.1=127.0.0.1:2887:3887
server.2=127.0.0.1:2888:3888
server.3=127.0.0.1:2889:3889
```
- 编辑`zoo3/conf/zoo.cfg`
```
dataDir=/usr/local/zookeeper/zoo3/data
clientPort=2183
server.1=127.0.0.1:2887:3887
server.2=127.0.0.1:2888:3888
server.3=127.0.0.1:2889:3889
```

- 配置`myid`的编号, 对应`server.1`, `server.2`, `server.3` 后面的数字
  - echo 1 >> /usr/local/zookeeper/zoo1/data/myid
  - echo 2 >> /usr/local/zookeeper/zoo2/data/myid
  - echo 3 >> /usr/local/zookeeper/zoo3/data/myid

- 备注:
  - 单机上的伪集群模式, server1,server2,server3 对应的端口号需要不同,clientPort也要不一致,多台机器则可以保持一致,方便维护

4. 启动服务
- `./zoo1/bin/zkServer.sh start`   
- `./zoo2/bin/zkServer.sh start`   
- `./zoo3/bin/zkServer.sh start`   

5. 查看状态
- `./zoo1/bin/zkServer.sh status`   
- `./zoo2/bin/zkServer.sh status`   
- `./zoo3/bin/zkServer.sh status`   

6. 连接测试
- `./zoo3/bin/zkCli.sh`
- 切换连接 `connect 127.0.0.1:2082`
- 创建节点 `create /home1`
- 获取数据 `get /home1`
- 其他
  - `ls /`
  - `ls2 /home1`

## 启动,停止脚本
- start_cluster.sh
```sh
#!/bin/bash

/usr/local/zookeeper/zoo1/bin/zkServer.sh start;
/usr/local/zookeeper/zoo2/bin/zkServer.sh start;
/usr/local/zookeeper/zoo3/bin/zkServer.sh start;
```

- stop_all.sh
```sh
#!/bin/bash

/usr/local/zookeeper/zoo1/bin/zkServer.sh stop;
/usr/local/zookeeper/zoo2/bin/zkServer.sh stop;
/usr/local/zookeeper/zoo3/bin/zkServer.sh stop;
```

## 阿里云服务器配置
- 启用 2081 等端口


## dubbo 连接zookeeper 集群配置
- 方式1 (测试可用)
> dubbo admin 配置连接集群 
```
dubbo.registry.address=zookeeper://127.0.0.1:2181?backup=127.0.0.1:2182,127.0.0.1:2183
```

- 方式2 (测试可用)
```
dubbo:
  application:
    name: demo-consumer # 消息者名字
  registry:
#    address: zookeeper://127.0.0.1:2181 # 注册中心地址
    address: 106.15.x.55:2181,106.15.x.55:2182,106.15.x.55:2183 # 注册中心地址
    protocol: zookeeper
```

## zookeeper 图形化的客户端工具：ZooInspector
- ZooInspector下载地址: <https://issues.apache.org/jira/secure/attachment/12436620/ZooInspector.zip>
- 解压，进入目录 `ZooInspector\build`
- 运行或mac下双击zookeeper-dev-ZooInspector.jar
```
java -jar zookeeper-dev-ZooInspector.jar //执行成功后，会弹出java ui client
```
- 点击左上角连接按钮，输入Zookeeper服务地址：ip:2181
- 点击OK，就可以查看Zookeeper节点信息啦