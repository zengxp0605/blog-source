---
title: nodejs中mysql异步事务的几点思考
comments: true
date: 2016-04-01 22:33:57
categories: [nodejs]
tags: [nodejs,mysql]
keywords: nodejs mysql异步事务
---
#### 说明:
- mysql 只在启动node 时,初始化一次连接, 通过 `global.app.set('db.test', mysqlConn)` 存储, 获取使用 `app.get('db.test')`;
- 所有的测试都是基于socket连接发送 emit 命令来执行的 
- 测试只使用一个`test` 表,同时当做日志表使用,
- `test` 表结构如下:  
    ```
    CREATE TABLE `test` (
      `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '自增长ID',
      `user_id` int(11) unsigned NOT NULL COMMENT '不允许为NULL,NULL 报错',
      `title` varchar(100) NOT NULL DEFAULT '' COMMENT 'title',
      `content` varchar(500) NOT NULL DEFAULT '' COMMENT 'content',
      `update_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '自动更新时间',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8 COMMENT='test';

    ```

### 使用原始的回调写法
1. 只执行两条sql语句(事务A), 例子来自 [node-mysql模块](https://www.npmjs.com/package/mysql#transactions)

```javascript
test.trans = function() {

    var connection = app.get('db.test');

    connection.beginTransaction(function(err) {
      if (err) { throw err; }
      var data1 = {
        user_id : 111,
        title: 'insert',
      };
      connection.query('INSERT INTO test SET ?', data1 , function(err, result) {
        if (err) {
          return connection.rollback(function() {
            console.log('rollback0, 部分失败!', err);
          });
        }

        var data2 = {
            user_id : 222,
            //user_id : null, // 此时将触发回滚
            title: 'Log:', 
            content: result.insertId + ' added',
        };

        /***********代码段1 start***************/
        connection.query('INSERT INTO test SET ?', data2, function(err, result) {
          if (err) {
            return connection.rollback(function() {
                console.log('rollback1, 部分失败!', err);
            });
          }  
          connection.commit(function(err) {
            if (err) {
              return connection.rollback(function() {
                  console.log('rollback2, 部分失败!', err);
              });
            }
            console.log('All success!');
          });
        });
        /***********代码段1 end***************/

      });
    });
};

```

2. 将上述的代码段1 修改为下面的语句, 批量insert, 但是只有最后一次才提交
  - 目的是为了制造延时较长时间才执行 commit 或者 rollback

```javascript

var i = 15;
while(i >= 0){
    (function(_i){
        setTimeout(function(){

            data2.user_id = _i;
            connection.query('INSERT INTO test SET ?', data2, function(err, result) {
                if (err) {
                    return connection.rollback(function() {
                        console.log('rollback1, 部分失败!', err);
                    });
                }  

                // 只有最后一次才commit
                if(_i != 15) return null; 
                
                connection.commit(function(err) {
                    if (err) {
                        return connection.rollback(function() {
                            console.log('rollback2, 部分失败!', err);
                        });
                    }
                    console.log('All success!');
                });

            });
        }, 1000 * _i); // 延时一直递增
    })(i);

    i --;
} // end while

```

 - 并且此时, 在上面的语句还没有commit 的时候, 另一个socket 连接执行了下面的代码(事务B),那么会造成什么后果呢?

```javascript

test.insert = function() {
    var connection = app.get('db.test');

    connection.beginTransaction(function(err) {
        if (err) { throw err; }
        var data1 = {
            user_id : 3333,
            title: 'insert 333',
        };
        connection.query('INSERT INTO test SET ?', data1 , function(err, result) {
            console.log('test.insert: ' + result.insertId);
            
            /***********代码段2 start***************/
            // connection.rollback();
            connection.commit(function(err) {
                if (err) {
                    return connection.rollback(function() {
                        console.log('rollback2, 部分失败!', err);
                    });
                }
                console.log('All success!');
            });
            /***********代码段2 end***************/

        });
        
    }); 
};

```

- ** 此时后面的事务将直接将前面while循环中已经`query` 的语句都提交并执行掉, while循环中未完成的
则将不在事务中 **

- 此时如果注释掉 代码段2(commit) 部分, 事务A 中已经 `query` 的语句都提交并执行掉, 未执行的则在新的事务中,等待下次提交

- 若是 代码段2(commit) 部分改成 `connection.rollback();` 将会触发事务A 提交, while 未执行的语句将是普通执行的语句,不在事务中

- ** 事务B 直接提交了事务A!!! 这个一开始一直没有理解怎么回事???
后来想想,node是单线程的, 但是这里都是在不同的回调函数中,应该也是在不同的子线程中执行的, 
为什么会出现这个情况呢?**



### 通过测试,发现问题在于数据库连接, 使用的都是同一个连接!!!!!

囧!!!!

<hr>

### MySql 事务相关知识
0. 锁:    
 - 锁的机制
    - 共享锁：由读表操作加上的锁，加锁后其他用户只能获取该表或行的共享锁，不能获取排它锁，也就是说只能读不能写
    - 排它锁：由写表操作加上的锁，加锁后其他用户不能获取该表或行的任何锁，典型是mysql事务中
 
 - 锁的范围
    - 行锁: 对某行记录加上锁
    - 表锁: 对整个表加上锁

1. 什么是事务   
  - 事务是一条或多条数据库操作语句的组合，具备ACID，4个特点。
  - 原子性：要不全部成功，要不全部撤销
  - 隔离性：事务之间相互独立，互不干扰
  - 一致性：数据库正确地改变状态后，数据库的一致性约束没有被破坏
  - 持久性：事务的提交结果，将持久保存在数据库中

2. 事务级别    
 - 各种事务级别的简述  

   | 隔离级别                           |   脏读  |   不可重复读   |   幻读  |   加锁读  |
   | ---------------------------------- | ------- | -------------- | ------- | --------- |
   | READ UNCOMMITTED(读取未提交内容)   |   是    |   是           |   是    |   否      |
   | READ COMMITTED (读取提交内容)      |   否    |   是           |   是    |   否      |
   | REPEATABLE READ  (可重读)          |   否    |   否           |   是    |   否      |
   | SERIALIZABLE  (可串行化)           |   否    |   否           |   否    |   是      |

- 名词解释:    
  - 第一类丢失更新：在没有事务隔离的情况下，两个事务都同时更新一行数据，但是第二个事务却中途失败退出， 导致对数据的两个修改都失效了。
  - 脏读：脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。
  - 不可重复读：是指在一个事务内，多次读同一数据。在这个事务还没有结束时，另外一个事务也访问该同一数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。
  - 第二类丢失更新：不可重复读的特例。有两个并发事务同时读取同一行数据，然后其中一个对它进行修改提交，而另一个也进行了修改提交。这就会造成第一次写操作失效。
  - 幻读：是指当事务不是独立执行时发生的一种现象，例如第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样。
  > 不可重复读的重点是修改，同样的条件，你读取过的数据，再次读取出来发现值不一样了
  > 幻读的重点在于新增或者删除，同样的条件，第 1 次和第 2 次读出来的记录数不一样
  > ** Mysql默认的事务隔离级别为repeatable_read **

3. 修改事务级别设置  
 - 全局修改，修改mysql.ini配置文件，在最后加上
    ```
    #可选参数有：READ-UNCOMMITTED, READ-COMMITTED, REPEATABLE-READ, SERIALIZABLE.
    [mysqld]
    transaction-isolation = REPEATABLE-READ
    ```
 - 对当前session修改，在登录mysql客户端后，执行命令：
    ```
    // 设置
    mysql> set session transaction isolation level read uncommitted;
    // 查看
    mysql> select @@TX_ISOLATION;   

    ```

4.参考:  
  - 锁和事务级别, [http://www.cnblogs.com/zemliu/archive/2012/06/17/2552301.html](http://www.cnblogs.com/zemliu/archive/2012/06/17/2552301.html)
   > 文章通过实例详述了 READ-UNCOMMITTED, READ-COMMITTED, REPEATABLE-READ, SERIALIZABLE 四种隔离级别时出现的现象  

