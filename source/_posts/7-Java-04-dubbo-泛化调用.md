---
title: Java 学习记录4-dubbo泛化调用,group分组
comments: true
date: 2019-10-23 20:30
categories: [Java]
tags: [Java]
---

# dubbo 泛化调用 及group使用

## `mapi-web` 
    - web应用,启动http服务
    - 使用dubbo方式调用`gateway`服务

### web请求调用说明
  - 普通调用
    - <http://localhost:8080/mapi/iotbmp.uploadData?id=123>
        - `/mapi/iotbmp.uploadData` 会调用 `iotbmp`应用下的`SyncDataHandleService.handleData` 方法,此方法再通过反射获取到其他的的 `UserReportService.uploadData`方法
    
  - 泛化调用
    - <http://localhost:8080/gen-api/iotecg.test2?id=123> 
        - `/gen-api/iotecg.test2` 会调用 `iotecg` 下的 `SyncDataHandleService.test2` 方法 
        - `/gen-api/iotbmp.test` 会调用 `iotbmp` 下的 `SyncDataHandleService.test` 方法

## `gateway`
    - 模拟网关应用, 负责其他 dubbo 服务的统一调用
   
## `baseinterface` 
    - 基础接口
    - 使用`mvn clean install`安装到本地maven仓库
        
## 子应用
    - `iotbmp`
        - 通过配置的方式实现服务`ServiceBean<SyncDataHandleService>` 以及`serviceBean.setGroup("iotbmp")`
    - `iotecg`
        - 通过`@Service(version = "1.0.0", group = "iotecg")` 注解实现服务

## 跨子应用调用, group 方式以实现
    - provider 在`application.yml` 中使用 `registry.group=iotecg`的方式配置子应用时,未能生效
    - provider 使用 `@Service(version="1.0.0", group="iotecg")` 的方式则可以
    - consumer 可以使用 `@Reference(version="1.0.0", group="iotecg")` 注解来实现
    - consumer 通过泛化调用时, 则使用 `reference.setGroup("iotecg")` 的方式实现
  
## 几个关键点记录
 - 反射调用
 ```java
public JSONObject handleData(HandleDataModel hdm) {
    logger.info("[iotbmp]-[handleData] hdm: {}" , hdm.toString());

    HashMap<String, String[]> handleType = new HashMap<String, String[]>();

    handleType.put("uploadData", new String[] {"userReportService", "uploadData"});
    handleType.put("test", new String[] {"userReportService", "test"});
    handleType.put("test2", new String[] {"syncDataHandleServiceImpl", "test2"});

    logger.info("[iotbmp]-[handleData] 方法: {},入参: {}" , hdm.getCmd(), hdm.getData() );
    JSONObject result= new JSONObject();

    try {
        String cmd = hdm.getCmd();
        String[] arr = handleType.get(cmd);
        String className = arr[0];
        String methodName = arr[1];
        Object cls = BeanUtil.getBean(className);
        Method method = cls.getClass().getMethod(methodName, JSONObject.class);
        Object res = method.invoke(cls, hdm.getData());
        result = (JSONObject) res;
    } catch (Exception e){
        e.printStackTrace();
    }

    return result;
}
 ```  
 
 - 泛化调用
```java
genericService.$invoke(methodName, invokeParamTyeps, invokeParams);
```  

## TODO: 真正的泛化调用
- 在comsumer 中没有提前加载需要使用的接口, 动态加载


## code @<https://github.com/zengxp0605/spring-dubbo-demo.git>

