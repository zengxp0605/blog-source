<<<<<<< HEAD
---
title: Vim 常用命令
comments: true
date: 2018-8-23 20:27
categories: [Vim]
tags: [Vim]
keywords: Vim 
---


# 批量替换
> 语法 =>    :[addr]s/源字符串/目的字符串/[option]
``` 
[addr] 表示检索范围，省略时表示当前行。
“1,20” ：表示从第1行到20行；
“%” ：表示整个文件，同“1,$”；
“. ,$” ：从当前行到文件尾；
 
s : 表示替换操作
 
[option] : 表示操作类型
g 表示全局替换; 
c 表示进行确认
p 表示替代结果逐行显示（Ctrl + L恢复屏幕）;
省略option时仅对每行第一个匹配串进行替换;
```

- 常用全局替换
```
:s/XXX/YYY/g    # XXX 全部替换为 YYY
```

- 指定行号
```
:21,$s/XXX/YYY/g   #  21行至末尾
```

- 删除所有空行
```
:g/^$/d
```

<!--more-->

# 查找
- 输入 ? 或 /，然后按n或N向后或向前查找





# 参考
=======
---
title: Vim 常用命令
comments: true
date: 2018-8-23 20:27
categories: [Vim]
tags: [Vim]
keywords: Vim 
---


# 批量替换
> 语法 =>    :[addr]s/源字符串/目的字符串/[option]
``` 
[addr] 表示检索范围，省略时表示当前行。
“1,20” ：表示从第1行到20行；
“%” ：表示整个文件，同“1,$”；
“. ,$” ：从当前行到文件尾；
 
s : 表示替换操作
 
[option] : 表示操作类型
g 表示全局替换; 
c 表示进行确认
p 表示替代结果逐行显示（Ctrl + L恢复屏幕）;
省略option时仅对每行第一个匹配串进行替换;
```

- 常用全局替换
```
:s/XXX/YYY/g    # XXX 全部替换为 YYY
```

- 指定行号
```
:21,$s/XXX/YYY/g   #  21行至末尾
```

- 删除所有空行
```
:g/^$/d
```

<!--more-->

# 查找
- 输入 ? 或 /，然后按n或N向后或向前查找





# 参考
>>>>>>> db6b3d19cc5377565824e65452db02d91fce437b
> https://www.cnblogs.com/beenoisy/p/4046074.html