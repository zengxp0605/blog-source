---
title: 每天学点js-代码块性能测试
comments: true
date: 2016-04-18 20:00
categories: [js]
tags: [每天学点js,js]
keywords: 
---


#### 快速的测量javascript的性能，我们可以使用console的方法，例如

```javascript
console.time(label) 和 console.timeEnd(label)

console.time("Array initialize");
var arr = new Array(100),
    len = arr.length,
    i;

for (i = 0; i < len; i++) {
    arr[i] = new Object();
};
console.timeEnd("Array initialize"); // Outputs: Array initialize: 0.711ms

```