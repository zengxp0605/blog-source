---
title: nodejs 日志输出-追踪文件名,方法名及行号
comments: true
date: 2016-11-16 20:13:54
categories: [nodejs]
tags: [nodejs]
keywords: nodejs 日志输出 tracer
---

> 参考库: [baryon/tracer](https://github.com/baryon/tracer)

## 通过Error对象追踪调用栈, 获取到调用方法所在的问文件,方法名称,及行号等

- 测试代码如下: 

```js
class Logger {
    constructor() {}

    static log(msg) {
        let stackInfoStr = stackInfo();
        msg = `${stackInfoStr.file}:${stackInfoStr.line} (${stackInfoStr.method}) ${msg}`;
        let time = (new Date).toLocaleString();
        console.log(time + ' ' + msg);
    }
}

/**
 * 追踪日志输出文件名,方法名,行号等信息
 * @returns Object
 */
function stackInfo() {
    var path = require('path');
    var stackReg = /at\s+(.*)\s+\((.*):(\d*):(\d*)\)/i;
    var stackReg2 = /at\s+()(.*):(\d*):(\d*)/i;
    var stacklist = (new Error()).stack.split('\n').slice(3);
    var s = stacklist[0];
    var sp = stackReg.exec(s) || stackReg2.exec(s);
    var data = {};
    if (sp && sp.length === 5) {
        data.method = sp[1];
        data.path = sp[2];
        data.line = sp[3];
        data.pos = sp[4];
        data.file = path.basename(data.path);
    }
    return data;
}

function testFun() {
    Logger.log('test string');
}

testFun();
// output: 2016-11-16 14:10:28 tmp.js:38 (testFun) test string

```