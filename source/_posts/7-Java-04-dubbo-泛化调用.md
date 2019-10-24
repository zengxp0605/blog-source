---
title: Java 学习记录4: dubbo泛化调用,group分组
comments: true
date: 2019-10-23 20:30
categories: [Java]
tags: [Java]
---

# dubbo 泛化调用 及group使用

## `mapi-web` 
    - web应用,启动http服务
    - 使用dubbo方式调用`gateway`服务
    - <http://localhost:8080/mapi/iotbmp?mt=test&id=123>
        - `mt=test2` 会调用 `iotbmp` 下的 `SyncDataHandleService.test2` 方法
        - `mt=test` 会调用 `iotbmp` 下的 `SyncDataHandleService.test` 方法
      - <http://localhost:8080/mapi/iotecg?mt=test&id=123>
             - `mt=test2` 会调用 `iotecg` 下的 `SyncDataHandleService.test2` 方法
             - `mt=test` 会调用 `iotecg` 下的 `SyncDataHandleService.test` 方法 

## `gateway`
    - 模拟网关应用, 负责其他 dubbo 服务的统一调用
   
## `baseinterface` 
    - 基础接口
    - 使用`mvn clean install`安装到本地maven仓库
        
## 子应用
    - `iotbmp`
    - `iotecg`

## TODO: 跨子应用调用, group 方式以实现
    - provider 在`application.yml` 中使用 `registry.group=iotecg`的方式配置子应用时,未能生效
    - provider 使用 `@Service(version="1.0.0", group="iotecg")` 的方式则可以
    - consumer 可以使用 `@Reference(version="1.0.0", group="iotecg")` 注解来实现
    - consumer 通过泛化调用时, 则使用 `reference.setGroup("iotecg")` 的方式实现


## code @<https://github.com/zengxp0605/spring-dubbo-demo.git>