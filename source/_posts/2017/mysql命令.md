---
layout: "post"
title: "mysql命令"
date: "2017-04-25 11:19"
author: "webkong"
categories:
- 数据库
tags:
- MySQL
---

## MySQL SQL语句


> 方便查找，备忘
> 虽趣舍万殊，静躁不同。

<!-- more -->

### 启动和登录

```
mysql.server start/stop

mysql -u root -p

```
### 数据库状态

```
查看数据库  show databases;
使用数据库  use databeseName;

```
### 操作数据库

```
新建数据库  create database;
删除数据库  drop database databaseName; commit;
使用数据库  use databeseName;
```
### 表状态

```
显示数据库有多少表  show tabels;
显示表结构  describe tableName;
```
### 新建表
```
创建新表 create table tabname(col1 type1 [not null] [primary key],col2 type2 [not null],..)
根据已有的表创建新表：
A：create table tab_new like tab_old (使用旧表创建新表)
B：create table tab_new as select col1,col2… from tab_old definition only
```
### 删除新表
```
drop table tabname
```
### 增加一个列
```
Alter table tabname add column col type
注：列增加后将不能删除。DB2中列加上后数据类型也不能改变，唯一能改变的是增加varchar类型的长度。
```
### 添加主键
```
Alter table tabname add primary key(col)
说明：删除主键： Alter table tabname drop primary key(col)
```
### 创建索引
```
create [unique] index idxname on tabname(col….)
删除索引：drop index idxname
注：索引是不可更改的，想更改必须删除重新建。
```
### 创建视图
```
create view viewname as select statement
删除视图：drop view viewname
```
### 几个简单的常用sql语句
```
选择：select * from table1 where 范围
插入：insert into table1(field1,field2) values(value1,value2)
删除：delete from table1 where 范围
更新：update table1 set field1=value1 where 范围
查找：select * from table1 where field1 like ’%value1%’ ---like的语法很精妙，查资料!
排序：select * from table1 order by field1,field2 [desc]
总数：select count as totalcount from table1
求和：select sum(field1) as sumvalue from table1
平均：select avg(field1) as avgvalue from table1
最大：select max(field1) as maxvalue from table1
最小：select min(field1) as minvalue from table1
```
### 高级查询运算词
#### UNION 运算符
UNION 运算符通过组合其他两个结果表（例如 TABLE1 和 TABLE2）并消去表中任何重复行而派生出一个结果表。当 ALL 随 UNION 一起使用时（即 UNION ALL），不消除重复行。两种情况下，派生表的每一行不是来自 TABLE1 就是来自 TABLE2。
#### EXCEPT 运算符
EXCEPT 运算符通过包括所有在 TABLE1 中但不在 TABLE2 中的行并消除所有重复行而派生出一个结果表。当 ALL 随 EXCEPT 一起使用时 (EXCEPT ALL)，不消除重复行。
#### INTERSECT 运算符
INTERSECT 运算符通过只包括 TABLE1 和 TABLE2 中都有的行并消除所有重复行而派生出一个结果表。当 ALL 随 INTERSECT 一起使用时 (INTERSECT ALL)，不消除重复行。
注：使用运算词的几个查询结果行必须是一致的。
### 使用外连接
#### left （outer） join
左外连接（左连接）：结果集几包括连接表的匹配行，也包括左连接表的所有行。
SQL: select a.a, a.b, a.c, b.c, b.d, b.f from a LEFT OUT JOIN b ON a.a = b.c
#### right （outer） join:
右外连接(右连接)：结果集既包括连接表的匹配连接行，也包括右连接表的所有行。
#### full/cross （outer） join
全外连接：不仅包括符号连接表的匹配行，还包括两个连接表中的所有记录。
#### 分组:Group by
  一张表，一旦分组完成后，查询后只能得到组相关的信息。
 组相关的信息：（统计信息） count,sum,max,min,avg  分组的标准)
    在SQLServer中分组时：不能以text,ntext,image类型的字段作为分组依据
 在selecte统计函数中的字段，不能和普通的字段放在一起；
#### 对数据库进行操作
 分离数据库： sp_detach_db;
 附加数据库：sp_attach_db 后接表明，附加需要完整的路径名
#### 如何修改数据库的名称
sp_renamedb 'old_name', 'new_name'

## 提升
### 复制表
```
(只复制结构,源表名：a 新表名：b)
法一：select * into b from a where 1<>1（仅用于SQlServer）
法二：select top 0 * into b from a
```
### 拷贝表
```
(拷贝数据,源表名：a 目标表名：b)
insert into b(a, b, c) select d,e,f from b;
```
### 跨数据库之间表的拷贝(具体数据使用绝对路径)
```
insert into b(a, b, c) select d,e,f from b in ‘具体数据库’ where 条件
例子：..from b in '"&Server.MapPath(".")&"\data.mdb" &"' where..
```
### 子查询
```
(表名1：a 表名2：b)
select a,b,c from a where a IN (select d from b )
select a,b,c from a where a IN (1,2,3)
```
### 显示文章、提交人和最后回复时间
```
select a.title,a.username,b.adddate from table a,(select max(adddate) adddate from table where table.title=a.title) b
```
### 外连接查询
```
(表名1：a 表名2：b)
select a.a, a.b, a.c, b.c, b.d, b.f from a LEFT OUT JOIN b ON a.a = b.c
```
### 在线视图查询
```
(表名1：a )
select * from (SELECT a,b,c FROM a) T where t.a > 1;
```
### between的用法
```
between限制查询数据范围时包括了边界值,not between不包括
select * from table1 where time between time1 and time2
select a,b,c, from table1 where a not between 数值1 and 数值2
```
### in 的使用方法
```
select * from table1 where a [not] in (‘值1’,’值2’,’值4’,’值6’)
```
### not exists
```
两张关联表，删除主表中已经在副表中没有的信息
delete from table1 where not exists ( select * from table2 where table1.field1=table2.field1 )
```
### 四表联查问题
```
select * from a left inner join b on a.a=b.b right inner join c on a.a=c.c inner join d on a.a=d.d where .....
```
### 日程安排提前五分钟提醒
```
SQL: select * from 日程安排 where datediff('minute',f开始时间,getdate())>5
```
### 一条sql 语句搞定数据库分页
```
select top 10 b.* from (select top 20 主键字段,排序字段 from 表名 order by 排序字段 desc) a,表名 b where b.主键字段 = a.主键字段 order by a.排序字段
具体实现：
关于数据库分页：
  declare @start int,@end int
  @sql  nvarchar(600)
  set @sql=’select top’+str(@end-@start+1)+’+from T where rid not in(select top’+str(@str-1)+’Rid from T where Rid>-1)’
  exec sp_executesql @sql
```
注意：在top后不能直接跟一个变量，所以在实际应用中只有这样的进行特殊的处理。Rid为一个标识列，如果top后还有具体的字段，这样做是非常有好处的。因为这样可以避免 top的字段如果是逻辑索引的，查询的结果后实际表中的不一致（逻辑索引中的数据有可能和数据表中的不一致，而查询时如果处在索引则首先查询索引）
### 前10条记录
```
select top 10 * form table1 where 范围
```
### 选择在每一组b值相同的数据中对应的a最大的记录的所有信息(类似这样的用法可以用于论坛每月排行榜,每月热销产品分析,按科目成绩排名,等等.)
select a,b,c from tablename ta where a=(select max(a) from tablename tb where tb.b=ta.b)
### 包括所有在 TableA 中但不在 TableB和TableC 中的行并消除所有重复行而派生出一个结果表
(select a from tableA ) except (select a from tableB) except (select a from tableC)
### 随机取出10条数据
select top 10 * from tablename order by newid()
### 随机选择记录
select newid()
### 删除重复记录
1),delete from tablename where id not in (select max(id) from tablename group by col1,col2,...)
2),select distinct * into temp from tablename
  delete from tablename
  insert into tablename select * from temp
评价： 这种操作牵连大量的数据的移动，这种做法不适合大容量但数据操作
3),例如：在一个外部表中导入数据，由于某些原因第一次只导入了一部分，但很难判断具体位置，这样只有在下一次全部导入，这样也就产生好多重复的字段，怎样删除重复字段
alter table tablename
--添加一个自增列
add  column_b int identity(1,1)
 delete from tablename where column_b not in(
select max(column_b)  from tablename group by column1,column2,...)
alter table tablename drop column column_b
### 列出数据库里所有的表名
select name from sysobjects where type='U' // U代表用户
### 列出表里的所有的列名
select name from syscolumns where id=object_id('TableName')
