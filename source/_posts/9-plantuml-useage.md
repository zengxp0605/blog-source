---
title: 使用Plantuml绘图语法
comments: true
date: 2020-07-07 22:33:57
categories: [工具使用]
tags: [工具使用]
keywords: Plantuml
---

## 类图(Class Diagram)

类关系：

例子：
```
@startuml
ClassA <-- ClassB:关联
ClassA <.. ClassB:依赖
ClassA o-- ClassB:聚集
ClassA <|-- ClassB:泛化
ClassA <|.. ClassB:实现
@enduml
```

成员访问权限标示：  
```
-			private
#			protected
~			package private
+			public
```

例子：
```
@startuml
class Dummy {
 -field1
 #field2
 ~method1()
 +method2()
}
@enduml
```






