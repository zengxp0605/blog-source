---
title: mybatis使用总结
comments: true
date: 2019-10-23 20:30
categories: [Java]
tags: [Java]
---


## mybatis 相关
> 半自动的ORM框架
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
