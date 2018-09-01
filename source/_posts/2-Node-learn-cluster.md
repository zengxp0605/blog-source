---
title: nodejs 深入理解cluster
comments: true
date: 2018-8-31 21:45
categories: [nodejs]
tags: [nodejs]
keywords: nodejs cluster
---

# 概述
node实例是单线程作业的。在服务端编程中，通常会创建多个node实例来处理客户端的请求，以此提升系统的吞吐率。对这样多个node实例，我们称之为cluster（集群）。

借助node的cluster模块，开发者可以在几乎不修改原有项目代码的前提下，获得集群服务带来的好处。

集群有以下两种常见的实现方案，而node自带的cluster模块，采用了方案二。

## 方案一：多个node实例+多个端口
集群内的node实例，各自监听不同的端口，再由反向代理实现请求到多个端口的分发。

- 优点：实现简单，各实例相对独立，这对服务稳定性有好处。
- 缺点：
  - 增加端口占用，进程之间通信比较麻烦。
  - 需要使用`lokijs`这样的内存数据库时会比较麻烦

## 方案二：主进程向子进程转发请求
集群内，创建一个主进程(master)，以及若干个子进程(worker)。由master监听客户端连接请求，并根据特定的策略，转发给worker。

- 优点：通常只占用一个端口，通信相对简单，转发策略更灵活。
- 缺点：
  - 实现相对复杂，对主进程的稳定性要求较高。
  - IPC通信效率问题 [参看下文](#span1)


**综合而言,方案一应该是性能更优方案,通过nginx配置upstream,多个端口对应不同的独立进程.**
对于多人对战,需要使用`lockjs`时,可以分配一个大厅服务器,从大厅进入到对战房间时,由大厅服务分配指定的node进程,确保同一个房间内对战的玩家都在同一个进程中,从而可以使用内存服务器;
如果是使用`redis`之类的nosql, 则不存在这个问题;
另一个方案是可以是用[共享内存](#span2)


## TODO: 
- 如何测试两者的性能优劣?
- 两种方案做压测

<!--more-->

# 简单示例
```js
const cluster = require('cluster');            // | | 
const http = require('http');                  // | | 
const numCPUs = require('os').cpus().length;   // | |    都执行了
                                               // | | 
if (cluster.isMaster) {                        // |-|-----------------
  // Fork workers.                             //   | 
  for (var i = 0; i < numCPUs; i++) {          //   | 
    cluster.fork();                            //   | 
  }                                            //   | 仅父进程执行
  cluster.on('exit', (worker) => {             //   | 
    console.log(`${worker.process.pid} died`); //   | 
  });
  cluster.workers[id].on('message', (msg) => {
      console.log('message from worker:', msg);
  });
  cluster.workers[id].on('internalMessage', (msg) => {
      console.log('internalMessage from worker:', msg);
  });                                          //   |
} else {                                       // |-------------------
  // Workers can share any TCP connection      // | 
  // In this case it is an HTTP server         // | 
  http.createServer((req, res) => {            // | 
    process.send({ msg: `test from ${process.pid}` });
    //  下面的消息,master无法监听到
    process.send({ cmd: 'NODE_TEST', act: 'test', msg: `test from ${process.pid}` });
    res.writeHead(200);                        // |   仅子进程执行
    res.end('hello world\n');                  // | 
  }).listen(8000);                             // | 
}                                              // |-------------------
                                               // | |
console.log('hello');                          // | |    都执行了
```

# cluster模块相应的问题

### master、worker如何通信
- master进程通过 cluster.fork() 来创建 worker进程。cluster.fork() 内部 是通过 child_process.fork() 来创建子进程。
- master进程、woker进程可以通过IPC通道进行通信

### 如何实现端口共享
> 前面的例子中，多个woker中创建的server监听了同个端口3000。
> net.js源码中的listen方法通过listenInCluster方法来区分是父进程还是子进程，不同进程的差异在listenInCluster方法中体现
> 可以做到这样的原因是: net模块中，对 listen() 方法进行了特殊处理。根据当前进程是master进程，还是worker进程：
1. master进程：在该端口上正常监听请求。（没做特殊处理）
2. **worker进程：创建server实例。然后通过IPC通道，向master进程发送消息，让master进程也创建 server 实例，并在该端口上监听请求。当请求进来时，master进程将请求转发给worker进程的server实例。**

> 也就是说：master进程监听特定端口，并将客户请求转发给worker进程。

### master、worker内部通信小技巧
> 进程间通信（Inter-process communication, IPC）
在开发过程中，我们会通过 `process.on('message', fn)` 来实现进程间通信。

前面提到，master进程、worker进程在server实例的创建过程中，也是通过IPC通道进行通信的。那会不会对我们的开发造成干扰呢？比如，收到一堆其实并不需要关心的消息？

答案肯定是不会,具体原因: 

**当发送的消息包含cmd字段，且改字段以`NODE_`作为前缀，则该消息会被视为内部保留的消息，不会通过`message`事件抛出，但可以通过监听`internalMessage`捕获。**

- 示例代码:(使用fork实现)

master.js
```js
let childProcess = require('child_process');

function fork() {
  let worker = childProcess.fork('./worker');
  worker.send({ msg: 'test start' });
  worker.on('message', (data) => {
    console.log('master receive message: ', data, process.pid);
  });

  worker.on('internalMessage', (data) => {
    console.log('internalMessage data: ', data);
  });
}

fork();
```

worker.js
```js
process.on('message', (data) => {
  console.log('worker receive message: ', data, process.pid);

  const message = {
    cmd: 'NODE_CLUSTER',
    act: 'queryServer'
  };
  const message2 = {
    cmd: 'normalCmd',
    act: 'message2'
  };
  const message3 = {
    cmd: 'NODE_TEST',
    act: 'message3'
  };

  process.send(message);
  process.send(message2);
  process.send(message3);
});
```

output:
```
worker receive message:  { msg: 'test start' } 16484
internalMessage data:  { cmd: 'NODE_CLUSTER', act: 'queryServer' }
master receive message:  { cmd: 'normalCmd', act: 'message2' } 14908
internalMessage data:  { cmd: 'NODE_TEST', act: 'message3' }
```

- 一个扩展问题: 在内置 IPC 建立之前父子进程如何通信?
Node.js 在启动子进程的时候，主进程先建立 IPC 频道，然后将 IPC 频道的 fd (文件描述符) 通过 process.env 环境变量（NODE_CHANNEL_FD）的方式传递给子进程，然后子进程通过 fd 连上 IPC 与父进程建立连接。


### 负载均衡问题load balance (如何将请求分发到多个worker) 
- **每当worker进程创建server实例来监听请求，都会通过IPC通道，在master上进行注册。当客户端请求到达，master会负责将请求转发给对应的worker。**

- 具体转发给哪个worker？这是由转发策略决定的。可以通过环境变量NODE_CLUSTER_SCHED_POLICY设置，
- round-robin是当前cluster的默认负载均衡处理模式（除了windows平台），如果要回到之前的模式，有两种方式，
  - 可以在cluster加载之后未调用其它cluster函数之前执行：cluster.schedulingPolicy = cluster.SCHED_NONE; 来设定。
  - 设置环境变量NODE_CLUSTER_SCHED_POLICY="none"，例如：NODE_CLUSTER_SCHED_POLICY="none" node b.js > a.log。 可选值为：'rr'和'none'。参考 [demo](https://github.com/cswuyg/node_exercise/tree/master/cluster_demo/)
 

### 进程监控问题
master进程不会自动管理worker进程的生死，如果worker被外界杀掉了，不会自动重启，只会给master进程发送‘exit’消息，开发者需要自己做好管理。

### 数据共享问题<span id="span2"></span>
各个worker进程之间是独立的，为了让多个worker进程共享数据（譬如用户session），一般的做法是在Node.js之外再搭建一个数据库，多个worker进程通过数据库做数据共享。

- 使用redis, memcache
- 使用共享内存(只适用于单机)
> 参考另一篇文章: [nodejs 几种内存类库的使用](/2018/08/30/2-Node-memory-cache/)

### Node.js 内建 IPC 的问题<span id="span1"></span>
前面提到 Node.js 的 IPC 在 Unix 上是基于 UDS 的，由于 UDS 不使用网络底层协议来通信绕过了一堆的安全检查等问题，所以有理论上来说速度应该是挺快的 flag。但是实际使用中我们发现 Node.js 内置的 IPC 存在很大的性能问题。

这里写一个简单的测试，功能是通过 Node.js 内置的 IPC 从主进程向子进程发送数据，每一份发 100 条数据，每条数据 1MB 大小：

1. IPC 通信测试代码    
master.js
```js
const child_process = require('child_process');

let child = child_process.fork('./child.js');
let data = Array(1024 * 1024).fill('0').join('');

setInterval(() => {
  let i = 100;
  while(i--) child.send(`${data}|${Date.now()}`);
}, 1000);
```

child.js
```js
let i = 0;

process.on('message', (str) => {
  let now = Date.now();
  let [data, time] = str.split('|')
  console.log(i++, now - Number(time));
});
```

2. Socket 通信测试代码  
server.js
```js
const axon = require('axon');
const sock = axon.socket('push');
let data = Array(1024 * 1024).fill('0').join('');
sock.bind(3000);
console.log('push server started');

setInterval(() => {
  let i = 100;
  while(i--) sock.send(`${data}|${Date.now()}`);
}, 1000);
```

client.js
```js
const axon = require('axon');
const sock = axon.socket('pull');
sock.connect(3000);

let i = 0;
sock.on('message', function(str){
  let now = Date.now();
  let [data, time] = (str+'').split('|')
  console.log(i++, now - Number(time));
});
```

**简而言之，Node.js 自带的 IPC 可能由于实现上的问题（尚未深究源码）在传输较大数据（例如 1MB以上，具体下限未做详细分析）是存在性能问题的，所以不推荐使用。如果进程间有频繁的数据交互推荐使用别的方案比如 socket 通信、MQ 传递（kafka 已实现一定程度的实时）、RPC（thrift、GRPC）等。**

**值得一提的是 node 应用每个进程各自一个端口性能也比 cluster 要好**

> 备注:
> 各个平台的测试结果不尽相同, win10下,socket很慢,ipc很快
> Linux下则是ipc明显比socket慢,调整传输的数据大小后, 仍然是使用socket有更快的速度

测试代码地址: [https://github.com/zengxp0605/nodejs/tree/master/cluster-ipc-demo](https://github.com/zengxp0605/nodejs/tree/master/cluster-ipc-demo)

# 参考:
> [Node.js cluster 踩坑小结](https://zhuanlan.zhihu.com/p/27069865)
> [Node.js进阶：cluster模块深入剖析](https://juejin.im/entry/5ad3eb536fb9a028d375db4e)
> [Node学习笔记](https://github.com/chyingp/nodejs-learning-guide)
> [Node.js的cluster模块——Web后端多进程服务](https://www.cnblogs.com/cswuyg/p/5193313.html)
> [测试cluster两种模式的负载均衡效果](https://github.com/cswuyg/node_exercise/tree/master/cluster_demo/%E6%B5%8B%E8%AF%95cluster%E4%B8%A4%E7%A7%8D%E6%A8%A1%E5%BC%8F%E7%9A%84%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E6%95%88%E6%9E%9C)
> [Node.js cluster模块解读](https://juejin.im/post/5b178f535188257d5902fdfa)

