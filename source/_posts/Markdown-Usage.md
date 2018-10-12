---
title:  Markdown 语法 
date: 2016-04-03 15:58:38
categories: [markdown]
tags: [markdown]
---

项目地址 : [https://github.com/pandao/editor.md](https://github.com/pandao/editor.md)
完整示例 : [https://pandao.github.io/editor.md/examples/full.html](https://pandao.github.io/editor.md/examples/full.html)



**目录 (Table of Contents)**

[TOCM]

[TOC]


--------------------------------------------------------------------------------------------
### 标题
- 代码如下:
```
# Heading 1
## Heading 2               
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6
```
- 效果如下:  
# Heading 1
## Heading 2               
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6


--------------------------------------------------------------------------------------------
#### 标题（用底线的形式）Heading (underline)
```
This is an H1
=============

This is an H2
-------------
```

This is an H1
=============

This is an H2
-------------


--------------------------------------------------------------------------------------------
### 字符效果和横线等

```                
----

~~删除线~~ <s>删除线（开启识别HTML标签时）</s>
*斜体字*      _斜体字_
**粗体**  __粗体__
***粗斜体*** ___粗斜体___

上标：X<sub>2</sub>，下标：O<sup>2</sup>

```
                
----

~~删除线~~ <s>删除线（开启识别HTML标签时）</s>  
*斜体字*      _斜体字_  
**粗体**  __粗体__  
***粗斜体*** ___粗斜体___  

上标：X<sub>2</sub>，下标：O<sup>2</sup>


### 图片
```
插入图片：
![alt text](http://path/to/img.jpg "title")
文字链接：

[Title](你的链接地址)

图片加链接：(其实就是 图片 和 文字链接 2种语法结合起来用。)
[![alt text](http://path/to/img.jpg "title")](你的链接地址)
```

--------------------------------------------------------------------------------------------
### 任务列表
```
- [x] GFM task list 1
- [x] GFM task list 2
- [ ] GFM task list 3
    - [ ] GFM task list 3-1
    - [ ] GFM task list 3-2
    - [ ] GFM task list 3-3
- [ ] GFM task list 4
    - [ ] GFM task list 4-1
    - [ ] GFM task list 4-2
```

- [x] GFM task list 1
- [x] GFM task list 2
- [ ] GFM task list 3
    - [ ] GFM task list 3-1
    - [ ] GFM task list 3-2
    - [ ] GFM task list 3-3
- [ ] GFM task list 4
    - [ ] GFM task list 4-1
    - [ ] GFM task list 4-2


--------------------------------------------------------------------------------------------
### 分页符 Page break
```
[========]
```


--------------------------------------------------------------------------------------------
### 绘制表格

- 代码如下:
```
| First Header  | Second Header | Third Header  |
| ------------- | ------------- | ------------- |
| Content Cell  | Content Cell  | Content Cell  |
| Content Cell  | Content Cell  | Content Cell  |

```
- 效果如下:

| First Header  | Second Header | Third Header  |
| ------------- | ------------- | ------------- |
| Content Cell  | Content Cell  | Content Cell  |
| Content Cell  | Content Cell  | Content Cell  |




--------------------------------------------------------------------------------------------
### 流程图

```flow
st=>start: 用户登陆
op=>operation: 登陆操作
cond=>condition: 登陆成功 Yes or No?
e=>end: 进入后台

st->op->cond
cond(yes)->e
cond(no)->op
```