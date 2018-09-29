---
title: nodejs 几种内存类库的使用
comments: true
date: 2018-8-30 23:14
categories: [nodejs]
tags: [nodejs]
keywords: 
---


# nodejs  `lokijs` 和 `node-shared-cache` 对比
> node-shared-cache 相关代码只在Linux 环境下验证通过

### `node-shared-cache` 
- 使用共享内存, Linux `/dev/shm` 下面能看到对应的文件
- fork的进程间可以实现内存共享
- 读取和写入时间相近
- 不占用Node内存,不影响 1.4G 内存上限
- 写入数据超过分配的内存则进程崩溃


### `lokijs`

- 不能跨进程使用
- 写入速度较慢, 使用 `by` 读取速度快
- 占用Node内存, 超过1.4G 则内存溢出


### 写入时间对比
```
The result is:

plain obj: 3828.536ms
shared cache: 9867.463ms
lokijs cache: 18202.798ms
```

### 读取时间对比
```
The result is:

read plain obj: 922.495ms
read lokijs cache: 2328.279ms
read shared cache: 9824.456ms
```

<!--more-->

# 代码
```js

const moment = require('moment');


const _shared = true;
const _loki = true;

// ### Setting property
{
    // test plain object
    var plain = {};
    console.time('plain obj');
    for (var i = 0; i < 1000000; i++) {
        plain['test' + i] = i;
    }
    console.timeEnd('plain obj');
}

mem();

if (_shared) {
    // test shared cache
    var cache = require('node-shared-cache');
    var obj = new cache.Cache("test", 1048576);
    console.time('shared cache');
    for (var i = 0; i < 1000000; i++) {
        obj['test' + i] = i;
    }
    console.timeEnd('shared cache');
}
mem();
if (_loki) {
    // test lokijs
    var lokidb = require('lokijs')
    var db = new lokidb('test_mem');

    var doc = db.addCollection('test', { unique: ['key'], autoupdate: true });
    // doc.insert({ time: Date.now() });
    console.time('lokijs cache');
    for (var i = 0; i < 1000000; i++) {
        let key = 'test' + i;
        doc.insert({ key });
    }
    console.timeEnd('lokijs cache');
}
mem();
/**
The result is:

plain obj: 3828.536ms
shared cache: 9867.463ms
lokijs cache: 18202.798ms
 */

// ### Getting property
if (_loki) {
    console.time('read lokijs cache');
    for (var i = 0; i < 1000000; i++) {
        let key = 'test' + i;
        let r = doc.by('key', key);
        // console.log('n - r', r);
    }
    console.timeEnd('read lokijs cache');
}
mem();
{
    console.time('read plain obj');
    for (var i = 0; i < 1000000; i++) {
        plain['test' + i];
    }
    console.timeEnd('read plain obj');
}
mem();
if (_shared) {
    console.time('read shared cache');
    for (var i = 0; i < 1000000; i++) {
        obj['test' + i];
    }
    console.timeEnd('read shared cache');
}
mem();

/**
The result is:

read lokijs cache: 2328.279ms
read plain obj: 922.495ms
read shared cache: 9824.456ms
*/

function time() {
    return moment().format('YYYYMMDD HH:mm:ss');
}

/**
 * 记录进程内存信息
 */
function mem() {
    let memory = process.memoryUsage(),
        rss = (memory.rss / 1024 / 1024).toFixed(2),
        heapTotal = (memory.heapTotal / 1024 / 1024).toFixed(2),
        heapUsed = (memory.heapUsed / 1024 / 1024).toFixed(2);
    // console.log(memory);
    console.info(`${time()} MEM rss: ${rss} MB heapTotal: ${heapTotal} MB heapUsed: ${heapUsed}`)
}
```

# 验证内存溢出

## lokijs 内存溢出
```js

setInterval(() => {
    mem();
}, 1000)

const lokidb = require('lokijs')
const db = new lokidb('test2');

let doc = db.addCollection('test', { unique: [], autoupdate: true });

global.arr = [];

setInterval(() => {
    var str = new Array(20000000).join('*');
    // global.arr.push(str);
    doc.insert({ str });
}, 200);
```

## 使用闭包时的内存溢出
> 一旦一个变量被任一个闭包使用了，它会在所有的闭包词法环境结束之后才被释放，这会导致内存泄漏。

```js
var run = function () {
    var str = new Array(100000000).join('*');
    var doSomethingWithStr = function () {
        if (str === 'something') {
            console.log("str was something");
        }
        // TODO: 这里需要及时释放局部变量, 否则会导致内存泄露
        // str = null;
    };
    doSomethingWithStr();
    var logIt = function () {
        // console.log('interval');
        var a = 'interval';
    }
    setInterval(logIt, 100);
};
setInterval(run, 1000);

```

## 验证跨进程共享内存
 master.js
```js
//*********** master.js ***************//
var cache = require('node-shared-cache');
var obj = new cache.Cache("test", 536870912);
// setting property
obj.foo = "bar";
// getting property
console.log(obj.foo);
// enumerating properties
// for (var k in obj);
// Object.keys(obj);

// dump current cache
console.log(cache.dump(obj));

let childProcess = require('child_process');

function fork() {
    let worker = childProcess.fork('./worker');
    worker.send({ msg: 'test start' });
    worker.on('message', (data) => {
        console.log('master receive message: ', data);
    });
}

fork();
setTimeout(() => {
    console.log(cache.dump(obj));
}, 5000);

```

worker.js
```js
//***********  worker.js ***************//
process.on('message', (data) => {
  console.log('worker receive message: ', data);
  process.send({ msg: 'from work' });

  setTimeout(() => {
    var cache = require('node-shared-cache');
    var obj = new cache.Cache("test", 536870912);
    console.log(cache.dump(obj), ' from -- worker');
    obj.tmp = {
      opp: 'aaa',
    };
  }, 500);
});

```

代码地址: [https://github.com/zengxp0605/nodejs/tree/master/memory-libs-compare](https://github.com/zengxp0605/nodejs/tree/master/memory-libs-compare)


# 参考:
> [Node内存限制与垃圾回收](https://juejin.im/post/5a4f1281f265da3e484ba02f)
> https://github.com/kyriosli/node-shared-cache
> https://github.com/techfort/LokiJS
