---
title:  Markdown 语法 
date: 2016-04-03 15:58:38
categories: [markdown]
tags: [markdown]
---

项目地址 : [https://github.com/pandao/editor.md](https://github.com/pandao/editor.md)
完整示例 : [https://pandao.github.io/editor.md/examples/full.html](https://pandao.github.io/editor.md/examples/full.html)


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