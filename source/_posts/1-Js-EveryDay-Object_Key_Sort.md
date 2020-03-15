---
title: 每天学点js-js对象属性排序
comments: true
date: 2016-8-8 10:35
categories: [js]
tags: [每天学点js,js]
keywords: js对象属性排序
---


### js中的对象根据属性排序后输出

```js
function sortObj(obj) {
    var keys = Object.keys(obj);

    keys.sort();

    var rs = {};
    for (var i = 0; i < keys.length; i++) {
        var _key = keys[i];
        rs[_key] = obj[_key];
    }

    return rs;
}

var obj = { x: 2, z: 1, y: 3 };

var sobj = sortObj(obj);

console.log(obj);    // 输出  {x:2，z:1, y:3}

// 排序完成
console.log(sobj);   //  {x: 2, y: 3, z: 1}

```
