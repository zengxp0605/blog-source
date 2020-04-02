---
title: java反射
comments: true
date: 2019-10-23 20:30
categories: [Java]
tags: [Java]
---


## Reflect
自java8开始，可以直接通过反射得到方法的参数名。取代了之前如arg0、arg1 等无含义的参数名称。不过这样有个条件：你必须手动在编译时开启-parameters 参数，否则还是获取不到。
```java
 Method method = clazz.getMethod("method1", String.class, String.class);
  //得到该方法参数信息数组
  Parameter[] parameters = method.getParameters();
  //遍历参数数组，依次输出参数名和参数类型
  Arrays.stream(parameters).forEach(p->{
      System.out.println(p.getName()+" : "+p.getType());
  });
```
