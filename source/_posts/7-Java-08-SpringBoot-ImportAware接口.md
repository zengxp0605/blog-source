---
title: SpringBoot ImportAware接口实现EnableXXX注解
comments: true
date: 2020-06-23 20:30
categories: [Java]
tags: [Java]
---

## 简单实现自己的EnableAnnoXJob注解

1. 定义注解 EnableAnnoXJob
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Import(MyAutoConfiguration.class)
public @interface EnableAnnoXJob {
    @AliasFor("basePackage")
    String value() default "";
    @AliasFor("value")
    String basePackage() default "";
    int aDefaultVal() default 1000;
}
```
<!-- more -->
注：@Import接口的作用和Spring的xml配置文件中的<import>标签类似，可以导入另一个注解了@Configuration的配置类，也就是说，如果项目中引用了一些第三方的类库，如Redisson库，其内部包含很多@Configuration注解的配置类，但是我的项目没有自动扫描他的包，那么就可以用@Import(XXX.class)来导入其配置类使其生效，参考@EnableRedissonHttpSession注解的实现

2. 实现ImportAware接口
```java

@Configuration
public class MyAutoConfiguration implements ImportAware {
    private AnnotationMetadata annotationMetadata;
    @Override
    public void setImportMetadata(AnnotationMetadata annotationMetadata) {
        System.out.println("annotationMetadata: " + annotationMetadata);
        this.annotationMetadata = annotationMetadata;
    }
    /**
     * 模拟其他类解析 EnableAnnoXJob注解的内容
     * @return
     */
    @Bean
    public TestContainer testContainer() {
        TestContainer testContainer = new TestContainer();

        Map<String, Object> attributesMap = annotationMetadata.getAnnotationAttributes(EnableAnnoXJob.class.getName());

        System.out.println("attributesMap: " + attributesMap);
        AnnotationAttributes enableAnnoXJob = AnnotationAttributes.fromMap(attributesMap);

        String basePackage = enableAnnoXJob.getString("basePackage");
        int aDefaultVal = enableAnnoXJob.getNumber("aDefaultVal");
        System.out.println("basePackage: " + basePackage);
        System.out.println("aDefaultVal: " + aDefaultVal);
        testContainer.setConfigPackage(basePackage);
        return testContainer;
    }
}
```

3. TestContainer 模拟的容器
```java
public class TestContainer {
    private String configPackage;

    public String getConfigPackage() {
        return configPackage;
    }

    public void setConfigPackage(String configPackage) {
        this.configPackage = configPackage;
    }
}

```

4. 启动类
```java
@EnableAnnoXJob("com.stan.testPackage")
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

## 启动 Application
output:
```log
annotationMetadata: org.springframework.core.type.StandardAnnotationMetadata@5c1bd44c
attributesMap: {value=com.stan.testPackage, basePackage=com.stan.testPackage, aDefaultVal=1000}
basePackage: com.stan.testPackage
aDefaultVal: 1000
```

## 比较完整的示例， 自定义注解实现任务调度服务
<https://github.com/zengxp0605/spring-boot-mydemo/tree/master/11_my_annotation/src/main/java/com/stan/enableAnno2>


## 参考
- <https://www.cnblogs.com/tianboblog/p/12658539.html>