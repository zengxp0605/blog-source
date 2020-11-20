---
title: 读Spring源码-循环依赖处理
comments: true
date: 2020-09-23 20:30
categories: [Java]
tags: [Java]
---

## 通过debug代码,绘制下图


![Spring处理循环依赖图示](http://plantuml.com/plantuml/svg/nLVFJnj75BxFNp6YXzgIYxZaaieLO8HKYRO8Ixsq71PxGhPQTbUpinJV73KGb0HAbOQqWQAYce9e7OBQKiUapZ_ZMPjJ_uM-cTaTXlKYA6rLBfOzt_lnlVc-Ds8qO1PbuOA3cCqSWii-jTrvthZVsrZXdSFU-b6tyrt_jqs-rUarsWWH5t0rFERxZbsncEsvs0h4Ltqo5p1gLPY1A7ak8qjC0iTAQU0uiTn9-FHeJIB69nF41BbZXGuhGqCEOPuha3DatWwOWawKnsTIs2aujEOSaaewCGLPqms6pFlSfgHa-Ua8rQWH4aEhOwxhCSb9mAUzW4rJhqA2mACgQ7nw6Y6WZjzvJwIuZIcvGqbmDvT7y5Ny2lnkutaYQUU6RBTAbig4BjeLCfH7S0WOIKo2DFKqybCNd_su6Vsyspjz4krikw7Z815r7ftxmFBpGHB_qTndfC82t6qr--k_yoytpNJTLbiaW9ovCc7dGK4ff1H6uDJUTqkKIDwGWAyseiscAWoXtTOQtpxWgrlyj0sdSHi9q_wRh_dM3aB58kQlwtp_aG9Yipuk5YLpDJCHLH7LIFQ80652grxf70npYHjfhvXn_EXL_tZtpzUF-V99612Q_7oy4zNtO_eoip5-grhLuu-5aSiNe42dRinM7zeaACZnKQbGGibvU01pYNuvu2kFpQO6gFYU7pen5MXga03V_OalBwkkS_q7X_pXWKYxl3Ug5MeCIAfHPDKgXG-VK5Rxs7924gXiiahl-Vs1iI3qDhHzzkpNtlQztjEzRglURMz5pRspmmOCKc4WJgqA98l9uKsfMvaXWUSiaWdY4wZWLceAc9HJyMgt88Y1ZZzvQsJargsHaTcqIOpMXl9AHKh4uk1op-Z0wre6WRa9flRyNOQki8-HEP7Fl-4T61KMJQ1C5vdaT0fPEsqlrPus5ngBvmj_avEpcJOJ8JTmcH5ccanfz_zocMxgQfCfw7_iCL44D2-7eZ9Vso-3FhdY3U4hQw1nOyd_hKTbC5g42Uq0xzaaoH96fbUYPYDwTd8J_KVD7qpzmuoqY53tTFqI1OYrL7_UV_DW3lX_Q2sGuaKYgds--Z8LMYiW_kf4XQQFXc4mokMvl8uVtC0vnPM_-4BlC76viRWBqJT7tTOXNtiOhMxm9_VoY6zy9KaGYpgrScgTPBuAXKo-lVtJisyR9kxINM7UiDeR_vY6af0MwysovWkmxI0pVm4BuW0GGhsnfBMJCd8dLj4MZZ6W4LNFi31aDKCbZpfGKVt1kfBEDNVXnGshbCZt_hZu8Ee5QffWODLWGGXKmzUUTZkFekFlCz-GOJy4YPdsoKGaqYxWr77-NJIea65A5hn5egC0bAMuB5pM8Xs6hMnWjDImyITdOZpQwdgk7ALygepl2WQQ0Ad9fogzMlbByurHgpXbJQqsC8fEaBfOkOUX6mKqTA9lxl8xbj2RNBBCg0QJfUm2Dl_3YXsXrhuQ24AZCFfmmVaB)

[查看大图](http://plantuml.com/plantuml/svg/nLVFJnj75BxFNp6YXzgIYxZaaieLO8HKYRO8Ixsq71PxGhPQTbUpinJV73KGb0HAbOQqWQAYce9e7OBQKiUapZ_ZMPjJ_uM-cTaTXlKYA6rLBfOzt_lnlVc-Ds8qO1PbuOA3cCqSWii-jTrvthZVsrZXdSFU-b6tyrt_jqs-rUarsWWH5t0rFERxZbsncEsvs0h4Ltqo5p1gLPY1A7ak8qjC0iTAQU0uiTn9-FHeJIB69nF41BbZXGuhGqCEOPuha3DatWwOWawKnsTIs2aujEOSaaewCGLPqms6pFlSfgHa-Ua8rQWH4aEhOwxhCSb9mAUzW4rJhqA2mACgQ7nw6Y6WZjzvJwIuZIcvGqbmDvT7y5Ny2lnkutaYQUU6RBTAbig4BjeLCfH7S0WOIKo2DFKqybCNd_su6Vsyspjz4krikw7Z815r7ftxmFBpGHB_qTndfC82t6qr--k_yoytpNJTLbiaW9ovCc7dGK4ff1H6uDJUTqkKIDwGWAyseiscAWoXtTOQtpxWgrlyj0sdSHi9q_wRh_dM3aB58kQlwtp_aG9Yipuk5YLpDJCHLH7LIFQ80652grxf70npYHjfhvXn_EXL_tZtpzUF-V99612Q_7oy4zNtO_eoip5-grhLuu-5aSiNe42dRinM7zeaACZnKQbGGibvU01pYNuvu2kFpQO6gFYU7pen5MXga03V_OalBwkkS_q7X_pXWKYxl3Ug5MeCIAfHPDKgXG-VK5Rxs7924gXiiahl-Vs1iI3qDhHzzkpNtlQztjEzRglURMz5pRspmmOCKc4WJgqA98l9uKsfMvaXWUSiaWdY4wZWLceAc9HJyMgt88Y1ZZzvQsJargsHaTcqIOpMXl9AHKh4uk1op-Z0wre6WRa9flRyNOQki8-HEP7Fl-4T61KMJQ1C5vdaT0fPEsqlrPus5ngBvmj_avEpcJOJ8JTmcH5ccanfz_zocMxgQfCfw7_iCL44D2-7eZ9Vso-3FhdY3U4hQw1nOyd_hKTbC5g42Uq0xzaaoH96fbUYPYDwTd8J_KVD7qpzmuoqY53tTFqI1OYrL7_UV_DW3lX_Q2sGuaKYgds--Z8LMYiW_kf4XQQFXc4mokMvl8uVtC0vnPM_-4BlC76viRWBqJT7tTOXNtiOhMxm9_VoY6zy9KaGYpgrScgTPBuAXKo-lVtJisyR9kxINM7UiDeR_vY6af0MwysovWkmxI0pVm4BuW0GGhsnfBMJCd8dLj4MZZ6W4LNFi31aDKCbZpfGKVt1kfBEDNVXnGshbCZt_hZu8Ee5QffWODLWGGXKmzUUTZkFekFlCz-GOJy4YPdsoKGaqYxWr77-NJIea65A5hn5egC0bAMuB5pM8Xs6hMnWjDImyITdOZpQwdgk7ALygepl2WQQ0Ad9fogzMlbByurHgpXbJQqsC8fEaBfOkOUX6mKqTA9lxl8xbj2RNBBCg0QJfUm2Dl_3YXsXrhuQ24AZCFfmmVaB)

<!-- more -->

## 代码如下：

其中UserService和IndexService相互依赖

```java
@Component
public class UserService {
	@Autowired
	private IndexService indexService;
	
	public UserService() {
		System.out.println("UserService constructor");
	}
}
```
```java
@Component
public class IndexService {
	@Autowired
	private UserService userService;
	
	public IndexService(){
		System.out.println("IndexService constructor");
	}

	public UserService getUserService() {
		return userService;
	}
}
```

```java
@Configuration
@ComponentScan("com.stan.demo")
public class AppConfig {
}
```

```java
public class Application {
	public static void main(String[] args) {
		//AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
		// or
		AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext();
		ac.register(AppConfig.class);
		// 这中间可以设置禁止循环依赖
//		ac.setAllowCircularReferences(false);
		ac.refresh();
		System.out.println(ac.getBean(IndexService.class).getUserService());
	}
}
```


<details title="plantuml">
<summary>plantuml-绘图文本</summary>
@startuml
title Spring处理循环依赖图示

entity Application as App
entity AbstractApplicationContext as AAC
entity AbstractBeanFactory as ABF
entity DefaultSingletonBeanRegistry as DSBR
entity DefaultListableBeanFactory as DLBF
entity AbstractAutowireCapableBeanFactory as AACBF
entity AutowiredAnnotationBeanPostProcessor as AABPP

App -> AAC: refresh()
AAC -> ABF: getBean("indexService")
ABF -> DSBR: getSingleton("indexService")\n 首次结果null
DSBR -> DSBR: (Map)singletonObjects 中获取indexService为null\n (Set)singletonsCurrentlyInCreation中判断结果为不在创建中
DSBR --> ABF: 返回

== 开始创建 indexService ==
ABF -> AACBF: createBean("indexService")
AACBF -> AACBF: doCreateBean()\n创建了indexService对象，其userService属性为null
AACBF -> DSBR: addSingletonFactory(),\n往(Map)singletonFactories,(Set)registeredSingletons中注册indexService
AACBF -> AACBF: populateBean为indexService对象填充属性(自动注入@Autowired)
AACBF -> AABPP: postProcessPropertyValues填充index的userService属性

'中间省略一些步骤，属性的处理
AABPP -> DLBF: doResolveDependency处理index的属性依赖
DLBF -[#005500]> ABF: getBean("userService")开始获取user
ABF -> DSBR: getSingleton("userService")\n 首次结果null
DSBR -> DSBR: 
note right
(Map)singletonObjects 中获取userService为null
(Set)singletonsCurrentlyInCreation中判断结果为不在创建中
end note

DSBR --> ABF: 返回getSingleton结果为null

== 开始创建userService ==

ABF -[#0000FF]> AACBF: createBean("userService")
AACBF -> AACBF: doCreateBean()\n创建了userService对象，其indexService属性为null
AACBF -> DSBR: addSingletonFactory(),\n往(Map)singletonFactories,(Set)registeredSingletons中注册userService
AACBF -> AACBF: populateBean为userService对象填充属性(自动注入@Autowired)
AACBF -> AABPP: postProcessPropertyValues填充user的index属性
AABPP -> DLBF: doResolveDependency处理属性依赖
DLBF -> ABF: getBean("indexService")再次获取index
ABF -> DSBR: getSingleton("indexService")\n 再次获取index
DSBR -[#red]> DSBR: x 

note right
此时(Set)singletonsCurrentlyInCreation中判断结果为正在创建中的对象，
从(Map)singletonFactories中通过beanName="indexService"获取到singletonFactory,
并通过singletonFactory.getObject()获取到indexService对象(此时它的属性user为null,是个半成品)
向(Map)earlySingletonObjects中注册indexService,singletonFactories中移除indexService
end note

DSBR --> ABF: getSingleton返回indexService对象(此时它的属性user为null,是个半成品)
ABF --> DLBF: 返回indexService对象(此时它的属性user为null),不是完全的Bean
DLBF --> AABPP: 返回indexService
AABPP --> AACBF: 返回，此时userService的indexService是一个对象了
AACBF -[#0000FF]-> ABF: 返回创建好的userService

ABF -[#red]> DSBR: 注册userService Bean到单例池(Map)singletonObjects.put()
note right
singletonObjects.put(beanName, singletonObject);
singletonFactories.remove(beanName);
earlySingletonObjects.remove(beanName);
registeredSingletons.add(beanName);
end note            

ABF -[#005500]-> AACBF: 返回创建好的userService

== userService创建完成 ==

AACBF -> AABPP: 将userService注入到indexService的属性中

AACBF --> ABF: 返回填充好属性的indexService Bean
ABF -[#red]> DSBR: 注册indexService Bean到单例池(Map)singletonObjects.put()
ABF --> AAC: 返回indexService, 循环依赖的Bean处理完成

@enduml
</details>


## 参考
[路神博客](https://blog.csdn.net/java_lyvee/article/details/101793774)
