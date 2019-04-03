---
title: SQL优化案例
comments: true
date: 2018-3-14 20:17
categories: [mysql]
tags: [mysql]
keywords: mysql
---

## 部分sql 优化案例

1. 子查询优化
--优化前SQL
select * from table_a a 
where a.id not in (select aid from table_b)

--优化后SQL
select * from table_a a
left join table_b b on a.id = b.aid  
where b.aid is null

2. WHERE条件中的数据类型不一致优化
例如table_a表中的order字段为字符型
--优化前SQL(全表扫描，不走索引)
select * from table_a where order=88888888888888;

--优化后SQL(走索引order)
select * from table_a where order='88888888888888';

3. WHERE条件中的过滤字段如需转换类型，只能转换过滤值，不能转换被过滤字段
--优化前SQL
select * from table_a where year(字段名)>=2017;

--优化后SQL
select * from table_a where 子段名>='2017-01-01';

4. 谨慎使用like
--优化前SQL（不走索引）
select * from table_a where 姓名 like '%李%';
select * from table_a where 姓名 like '%李';

--优化后SQL（走索引）
select * from table_a where 姓名 like '李%';

5. 批量插入优化
--优化前
insert into table_a(id,order) values(1,'66666');
insert into table_a(id,order) values(2,'66666');

--优化后
insert into table_a(id,order) values(1,'66666'),values(2,'66666');

6. 关联表的关联字段保持数据类型一致
--如果a表的userID和b表的userID类型不一致会导致不走索引
select * from table_a a
left join table_b b on a.userid = b.userid
where ...

7. 组合索引的字段顺序不可随意修改
例如以下两个索引A和B是不同的
索引A：mall_order(userid,add_time)
索引B：mall_order(add_time,userid)
--案例如下：
SELECT * FROM mall_order WHERE userid=123456;
这张表的索引如果加索引A的话可以正常走索引，如果加索引B的话无法走索引。

