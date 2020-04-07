---
title: Css 多列等高设置技巧
date: 2016-11-12 12:01
categories: [Git]
tags: Css
---

## Css 多列等高设置技巧

> 参考: [http://www.cnblogs.com/2050/archive/2012/07/31/2616460.html](http://www.cnblogs.com/2050/archive/2012/07/31/2616460.html)

### 布局方案

等高布局有几种不同的方法，但目前为止我认为浏览器兼容最好最简便的应该是padding补偿法。首先把列的padding-bottom设为一个足够大的值，再把列的margin-bottom设一个与前面的padding-bottom的正值相抵消的负值，父容器设置超出隐藏，这样子父容器的高度就还是它里面的列没有设定padding-bottom时的高度，当它里面的任一列高度增加了，则父容器的高度被撑到它里面最高那列的高度，其他比这列矮的列则会用它们的padding-bottom来补偿这部分高度差。因为背景是可以用在padding占用的空间里的，而且边框也是跟随padding变化的，所以就成功的完成了一个障眼法。
<!-- more -->

- 效果图

![效果图](/assets/images/3-css-1.png "css多列等高")

- 代码如下  

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<title>高度自适应布局</title>
<style>
body{ padding:0; margin:0; color:#f00;}
.container{ margin:0 auto; width:600px; border:3px solid #00C;
    overflow:hidden; /*这个超出隐藏的声明在IE6里不写也是可以的*/
}
.left{ float:left; width:150px; background:#B0B0B0;
    padding-bottom:2000px;
    margin-bottom:-2000px;
}
.right{ float:left; width:450px; background:#6CC;
   padding-bottom:2000px;
   margin-bottom:-2000px;
}
</style>
</head>
<body>
<div class="container">
    <div class="left">我是left</div>
    <div class="right">我是right<br><br><br>现在我的高度比left高，但left用它的padding-bottom补偿了这部分高度</div>
    <div style="clear:both;"></div>

</div>

<div style="clear:both;background:pink;">
	<p> 我是后面的内容...</p>
</div>
</body>
</html>
```