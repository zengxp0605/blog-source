---
title: Java 学习记录-3
comments: true
date: 2019-09-02 10:57
categories: [Java]
tags: [Java]
---

## mybatis 相关
- 获取insert 返回的自增长id

注解的方式,在接口映射器中设置useGeneratedKeys参数
```java
@Options(useGeneratedKeys = true, keyProperty = "id", keyColumn = "id")
@Insert("insert into test(name,descr,url,create_time,update_time) values(#{name},#{descr},#{url},now(),now())")
```

xml配置,在xml映射器中配置useGeneratedKeys参数
```xml
<insert id="insertOneTest" parameterType="com.jason.mybatisxml.model.User" useGeneratedKeys="true" keyProperty="id" keyColumn="id">
    INSERT INTO
        users
        (userName,passWord,user_sex,nick_name)
    VALUES
        (#{userName}, #{passWord}, #{userSex}, #{nickName})
</insert>
```


# 下一步计划
- dubbo -- [ok]
- druid
- zookeeper -- [ok]
- RabbitMQ -- [ok]
- Docker
- AOP(面向切面编程) `@Aspect`

# 待阅读
- [基于Netty自己动手实现RPC框架](https://zhuanlan.zhihu.com/p/35720383)
- [基于Netty自己动手实现Web框架](https://zhuanlan.zhihu.com/p/36064672)
  - [httpkids](https://github.com/pyloque/httpkids)

- 待学习的项目
  - [基于springboot+dubbo+mybatis的分布式敏捷开发框架](https://github.com/G-little/priest)









