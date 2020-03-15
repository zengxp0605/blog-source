---
title: Redis与Memcache的对比
comments: true
date: 2016-01-12 22:33:57
categories: [Redis]
tags: [redis,memcache]
keywords: Redis与Memcache的对比
---

1. 它们都是C语言开发的(key-value)内存数据存储 

2. 线程:
 - redis 是单线程,使用epoll 多路复用io模型,非阻塞
 - memcache 为多线程,依赖libevent

3. 锁
 - redis 中没有使用锁, 可以使用watch 命令添加乐观锁,一般结合事务使用 `multi ... exec  ||  discard`  
    - watch命令会监视给定的key,当exec时候如果监视的key从调用watch后发生过变化，则整个事务会失败。也可以调用watch多次监视多个key.这 样就可以对指定的key加乐观锁了。注意watch的key是对整个连接有效的，事务也一样。如果连接断开，监视和事务都会被自动清除。当然了exec,discard,unwatch命令都会清除连接中的所有监视。
    - redis只能保证事务的每个命令连续执行，但是如果事务中的一个命令失败了，并不回滚其他命令
  
 - 乐观锁：  
    - 大多数是基于数据版本(version)的记录机制实现的。数据版本即为数据增加一个版本标识，在基于数据库表的版本解决方案中，一般是通过为数据库表添加一个 “version”字段来实现读取出数据时，将此版本号一同读出，之后更新时，对此版本号加1。
    - 此时，将提交数据的版本号与数据库表对应记录的当前版本号进行比对，如果提交的数据版本号大于数据库表当前版本号，则予以更新，否则认为是过期数据。
  
 - memcache是一个内存缓存，key的长度小于250字符，单个item存储要小于1M，不适合虚拟机使用

 - 数据一致性不同  
    - redis使用的是单线程模型，保证了数据按顺序提交。
    - memcache需要使用cas保证数据一致性。CAS（Check and Set）是一个确保并发一致性的机制，属于“乐观锁”范畴；原理很简单：拿版本号，操作，对比版本号，如果一致就操作，不一致就放弃任何操作

 - cpu利用  
    - redis单线程模型只能使用一个cpu，可以开启多个redis进程

4. incr & decr
 - Redis支持的整数型Value值的具体类型为带符号64-bit整数，而Memcached支持的整数型Value值的具体类型为无符号64-bit整数，这就导致两个系统支持的整数型Value值的取值范围是不一样的。Redis支持的整数型Value值范围是-9223372036854775808~9223372036854775807，而Memcached的范围则是0~18446744073709551615。
 - Redis 当数值达到最大或者最小极值时,再incr/decr 操作会报错(error)ERR increment or decrement would overflow
 - Memcache 当达到最大值时,incr key 1, 那么key=0; 当key=0时,decr key 1, 此时key仍然为0

5. 整体对比
 - 性能对比：由于Redis只使用单核，而Memcached可以使用多核，所以平均每一个核上Redis在存储小数据时比Memcached性 能更高。而在100k以上的数据中，Memcached性能要高于Redis，虽然Redis最近也在存储大数据的性能上进行优化，但是比起 Memcached，还是稍有逊色。
 - 内存使用效率对比：使用简单的key-value存储的话，Memcached的内存利用率更高，而如果Redis采用hash结构来做key-value存储，由于其组合式的压缩，其内存利用率会高于Memcached。
 - Redis支持服务器端的数据操作：Redis相比Memcached来说，拥有更多的数据结构和并支持更丰富的数据操作，通常在 Memcached 里，你需要将数据拿到客户端来进行类似的修改再set回去。这大大增加了网络IO的次数和数据体积。在Redis中，这些复杂的操作通常和一般的 GET/SET一样高效。所以，如果需要缓存能够支持更复杂的结构和操作，那么Redis会是不错的选择。 

6. Redis 快速的原因
 - 绝大部分请求是纯粹的内存操作（非常快速） 
 - 采用单线程,避免了不必要的上下文切换和竞争条件 
 - 非阻塞IO,内部实现采用epoll，采用了epoll+自己实现的简单的事件框架。epoll中的读、写、关闭、连接都转化成了事件，然后利用epoll的多路复用特性，绝不在io上浪费一点时间 
 
  ps: 这3个条件不是相互独立的，特别是第一条，如果请求都是耗时的，采用单线程吞吐量及性能可想而知了。应该说redis为特殊的场景选择了合适的技术方案。

7. Redis 持久化

 - rdb  内存快照直接 dump到 dump.rdb 文件中
    - 在 Redis 运行时， RDB 程序将当前内存中的数据库快照保存到磁盘文件中， 在 Redis 重启动时， RDB 程序可以通过载入 RDB 文件来还原数据库的状态。
    - save 命令阻塞主进程, bgsave 命令fork一个子进程来执行保存操作,因此不阻塞主进程

 - aof 执行的命令追加到这个文件中(可以通过重写来优化:bgrewriteaof)  AppendOnlyFile   
    - AOF 文件通过保存所有修改数据库的命令来记录数据库的状态。
    - AOF 文件中的所有命令都以 Redis 通讯协议的格式保存。
    - 不同的 AOF 保存模式对数据的安全性、以及 Redis 的性能有很大的影响。
    - AOF 重写的目的是用更小的体积来保存数据库状态，整个重写过程基本上不影响 Redis 主进程处理命令请求。
    - AOF 重写是一个有歧义的名字，实际的重写工作是针对数据库的当前值来进行的，程序既不读写、也不使用原有的 AOF 文件。
    - AOF 可以由用户手动触发，也可以由服务器自动触发。

8. Redis 数据类型
 - String
 - Set
 - Sorted Set
 - List
 - Hash


#### 参考:
1. Redis的设计与实现 http://redisbook.readthedocs.org/en/latest/index.html
2. redis和memcache的对比 http://blog.csdn.net/u011386690/article/details/17469007
