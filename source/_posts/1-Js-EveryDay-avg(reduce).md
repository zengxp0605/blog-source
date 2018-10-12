---
title: 每天学点js-求平均值
comments: true
date: 2016-04-13 20:30
categories: [js]
tags: [每天学点js,js]
keywords: js求平均值
---

### 取平均值

要取得平均值，我们需要将数字求和，然后除以values的数目，步骤如下：

 - 取得数组长度(length)
 - 求和(sum)
 - 取得平均值(sum/length)

```javascript
let values = [2, 56, 3, 41, 0, 4, 100, 23];
let sum = values.reduce((previous, current) => current += previous);
let avg = sum / values.length;
// avg = 28
```


#### 取得中值的步骤是：

 - 将数组排序
 - 取得中位数

```javascript
let values = [2, 56, 3, 41, 0, 4, 100, 23];
values.sort((a, b) => a - b);
let lowMiddle = Math.floor((values.length - 1) / 2);
let highMiddle = Math.ceil((values.length - 1) / 2);
let median = (values[lowMiddle] + values[highMiddle]) / 2;
// median = 13,5
```
或者使用无符号右移操作符：(不太理解!!)

```javascript
let values = [2, 56, 3, 41, 0, 4, 100, 23];
values.sort((a, b) => a - b);
let median = (values[(values.length - 1) >> 1] + values[values.length >> 1]) / 2
// median = 23
```