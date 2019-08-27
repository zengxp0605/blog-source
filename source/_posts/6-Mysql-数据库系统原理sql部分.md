---
title: 数据库系统原理sql
comments: true
date: 2019-8-22 15:23
categories: [mysql]
tags: [mysql]
keywords: mysql
---

### 基础

```sql
-- 创建表
drop table if exists rank;
CREATE TABLE `rank` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_id` int(11) NOT NULL DEFAULT '0' COMMENT '用户Id',
  `nickname` varchar(100) NOT NULL DEFAULT '' COMMENT '玩家昵称',
  `rank` int(11) NOT NULL DEFAULT '0' COMMENT '排名',
  `round_id` varchar(100) NOT NULL DEFAULT '' COMMENT '场次Id',
  `score` int(11) NOT NULL DEFAULT '0' COMMENT '玩家分数',
  `reward_content` varchar(100) NOT NULL DEFAULT '' COMMENT '奖励内容',
  `coin_flow_id` varchar(100) NOT NULL DEFAULT '' COMMENT '货币订单流水号(金币等需要记录)',
  `reward_status` tinyint(2) NOT NULL DEFAULT '0' COMMENT '奖励发放状态',
  `delete_flag` tinyint(2) NOT NULL DEFAULT '0' COMMENT '删除标示 0:未删除 1:已删除',
  `raw_add_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '记录添加时间',
  `raw_update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '记录修改时间',
  PRIMARY KEY (`id`),
  KEY `idx_rank_round_id` (`round_id`),
  KEY `idx_rank_user_id_raw_add_time` (`user_id`,`raw_add_time`),
  KEY `idx_rank_user_id_round_id` (`user_id`,`round_id`)
) ENGINE=InnoDB COMMENT='积分活动排名奖励记录';


-- 创建普通索引
create index idx_score on rank(score asc);
alter table rank add index idx_rank(`rank`);

-- 显示索引
show index in rank;

-- 删除索引
drop index idx_rank on rank;
alter table rank drop index idx_score;

-- 删除主键, 需要先删除自增长
alter table rank change `id` `id` int(10);
alter table rank drop primary key;

-- 添加自增长主键
alter table rank add primary key(id);
alter table rank change `id` `id` int(11) not null auto_increment;


-- 删除数据
delete from rank where id > 0;

-- 插入数据
insert into rank values (0, 1001, 'xiao hong', 1, 11111, 100, '', '', 0, 0, now(), now()),
(0, 1002, 'xiao ming', 2, 11111, 60, '', '', 0, 0, now(), now());

insert into rank set user_id = 1003, nickname='xiao li';


select * from rank;

-- case 语句
select nickname as 姓名,score,
case 
when score=100 then '满分'
when score=0 then '缺失'
else score
end as 分数
from `rank`;


-- having 筛选
select *,count(*) as cnt from `rank`
group by round_id
having cnt > 1;
```

#### 视图
```sql
-- 删除视图
drop VIEW if exists rank_view;

-- 创建视图
create or replace view rank_view 
as 
select * from rank where score >0 with check option;

-- 查询视图
select * from rank_view;

-- 显示视图结构
show create view rank_view;
```

### 存储函数
```sql
drop function if exists fn_search;

delimiter $$
create function fn_search(uid int)
returns char(50)
deterministic
BEGIN
declare tmpScore int;
select score into tmpScore from `rank` where `user_id`=uid;
if tmpScore is null then
	return (select 'no');
else if tmpScore=0 THEN
	return (select '0 fen');
else if tmpScore=100 THEN
	return (select 'full ');
else 
	return (select tmpScore);
end if;
end if;
end if;
END 

-- 调用
select fn_search(1004);
```

### 存储过程
```sql
drop procedure if exists slt_score;

create procedure slt_score(IN iscore int)
BEGIN
	select * from rank where `score` > iscore;
END
-- 调用
call slt_score(0);
```

### 触发器
```sql
drop trigger if exists tri_rank_insert;

create trigger tri_rank_insert after insert on `rank`
for each ROW
BEGIN
	if NEW.score<0 or NEW.score>100 THEN
		-- delete from `rank` where score=NEW.score;
		set @str=NEW.id;
	end if;
END

-- 测试触发器
insert into `rank` set user_id=10000, nickname ='tes222',score=103;
select @str;
select * from rank;
```


### 用户相关
```sql
select user,password from mysql.user;

-- 删除用户
drop user lisi@localhost;

-- 创建用户
create user 'lisi'@'localhost' identified by '123456';

-- 重命名
rename user 'lisi'@'localhost' to 'zhangsan'@'localhost';

-- 修改密码
set password for 'zhangsan'@'localhost'=password('123');


-- 设置权限, 同时创建用户
grant select,update on test.rank
to 'wangwu'@'localhost' identified by '123',
'liming'@'localhost' identified by '123';

-- 其他权限
grant all on test.*
to 'wangwu'@'localhost';

grant create user on *.*
to 'liming'@'localhost';

-- 撤销权限
revoke all on *.* from 'wangwu'@'localhost';
```

########################## 关系代数操作 ######################

1. 5种基本的查询操作: 选择,投影,并,差,笛卡尔积
 - 可推导出: 连接,除,交
2. 关系代数
  - 传统集合运算: 并,差,交,笛卡尔积
  - 专门的关系运算: 选择,投影,连接,除

```sql
create table if not exists tb_r(
	a varchar(64),
	b varchar(64),
	c varchar(64)
);

create table if not exists tb_s(
	a varchar(64),
	b varchar(64),
	c varchar(64)
);

insert into tb_r values
('a1', 'b1', 'c1'),
('a1', 'b2', 'c2'),
('a2', 'b2', 'c1');

insert into tb_s values 
('a1', 'b1', 'c1'),
('a1', 'b2', 'c2'),
('a1', 'b3', 'c2'),
('a2', 'b2', 'c1');

select * from tb_r;
select * from tb_s;

-- 笛卡尔积 x
select * from tb_r,tb_s;
select * from tb_r cross join tb_s;

-- 投影, 去除重复???
select c from tb_s;


-- 并 ∪, 去除重复
select * from tb_r union select * from tb_s;

-- 交 ∩


-- 差 -
select * from tb_r right join tb_s on tb_r.b=tb_s.b where tb_r.b is null;
```
