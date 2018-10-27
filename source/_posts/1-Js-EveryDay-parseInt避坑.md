---
title: 每天学点js-parseInt避坑
comments: true
date: 2018-9-1 20:06
categories: [js]
tags: [每天学点js,js]
keywords: parseInt
---


# 前言

最近看到一篇博客： [这些JavaScript编程黑科技，装逼指南，高逼格代码，让你惊叹不已。](https://github.com/jawil/blog/issues/24)
上面介绍了挺多让人大开眼界，逼格满满的代码。其中有一句就是parseInt(0.0000008) === 8，结果返回的是true，这里我就详细解释下为什么parseInt(0.0000008)的结果等于8。

# 示例

parseInt语法:
```js
 parseInt(value, radix?)
```

## 一些例子
```js
parseInt('010'); // 10
parseInt(010); // 8
parseInt('0109') // 109
parseInt('0xA', 16) //10
parseInt('A', 16) //10
parseInt('   1   2323') // 1
parseInt('  abc1abc2323') // NaN
parseInt('  **1abc2323') // NaN
parseInt('    1abc2323') // 1
parseInt(' 12.9999') // 12
parseInt("  0x11")  // 17
 parseInt(1000000000000000000000.5, 10) // 1 ???
 parseInt(0.0000008, 10) // 8 ???
```

## 从中可以看出几条规则：
1. 字符串左边的空格会被忽略掉
2. 从左到右依次取数字字符串，如果最左边以非数字开头则返回NaN
3. 取到第一个数字字符后继续，直到取到一个非数字的字符截止（包括小数点），将这个时候得到的数字字符串转换为数字
4. 以'0x'开头的字符串会做特殊处理表示将后续的数字转换为16进制的整数

## 为什么parseInt(0.0000008)的结果等于8

 **因为调用parseInt方法时，会隐式地将传入的数字类型用toString方法转换为字符串！**
 而将0.0000008调用toString的结果会是"8e-7"，在javascript中，小于0.000001的浮点数会以科学计数法来表示，而这样的字符串参照上面的规则，自然得到的结果就是8了。

同样的, `1000000000000000000000.5` toString 之后为 "1e+21" 然后  `parseInt('1e+21')` 结果为1


# 结语
综上所述用parseInt转换数字的时候坑还是有的，使用时要多加注意。
可以使用 Math.round 或者 Math.floor 来替代 parseInt

```js
Math.round(0.0000008); // 8
```

```js
function ToInteger(x) {
    x = Number(x);
    return x < 0 ? Math.ceil(x) : Math.floor(x);
}
```

或者使用位运算
```js
0.0000008 | 0; // 0
~~0.0000008;
0.0000008 >> 0;
```


# 参考
> [为什么parseInt(0.0000008) === 8？](https://www.jianshu.com/p/4eea34b69aaa)
> [parseInt() doesn’t always correctly convert to integer](http://2ality.com/2013/01/parseint.html)
