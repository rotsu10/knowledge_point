### 第二章 SQL

#### DDL-------数据库操作

```
show databases ;		   显示当前数据库服务器中所有可用的数据库。
create database 数据库名;	创建一个新的数据库。
use 数据库名;				选择一个数据库作为当前操作的目标。
select database();		   查看当前正在使用的数据库名称。
drop database 数据库名;		永久删除指定的数据库（包括其中的所有表和数据）。
```

#### DDL-------**表操作**

```
show tables ;  											 展示表
create table 表名(字段 字段类型，字段 字段类型);   			创建表
desc 表名;  												查看表中字段
show create table 表名;   								查看表的建表语句
alter table 表名 add/modify/change/drop/rename to ...;    修改表结构
drop table 表名;							  				删除表
```

####  DML------操作数据库中的数据

```
INSERT INTO 表名 (列1, 列2, ...) VALUES (值1, 值2, ...); 	添加数据
SELECT 列1, 列2, ... FROM 表名 WHERE 条件;				选择数据
UPDATE 表名 SET 列1=值1, 列2=值2, ... WHERE 条件;		   修改语句
DELETE FROM 表名 WHERE 条件;							  删除数据
```





#### **DQL 执行语句**

select 

​	字段列表 ---->as别名

 from 

​	表名列表

 where 

​	条件列表 -----> like betwwen...and in > = < is null and or 分组前

group by 

​	分组字段列表

 having

​	 分组后字段列表

ordered by

​	 排序字段列表 -----> asc升序 desc降序

 limit 

​	分页参数------>  (起始索引，每一页所引述) 

#### **DQL执行顺序**

 from 表明列表  where 条件列表  group by 分组字段列表 select 字段列表 ordered by 排序字段列表  limit 分页参数





#### **DCL语句**--------- **数据控制语言**

管理数据库用户，控制数据库访问权限。

**1.用户管理**

查询用户

```
use mysql;
select * from mysql.user;
```

创建用户

```
create user 'itcast'@'localhost' identified by '123456';
create user 'heima'@'%' identified by '123456';
create user '用户名' @ '主机名' identified by '密码';
```

修改用户密码

```
alter user '用户名' @ '主机名' identified with mysql_native_password by '新密码';
```

删除用户

```
drop user '用户名' @ '主机名';
```

**2.权限控制**

all 所有权限	select 查询	insert 插入	update 更改

delete 删除	alter 修改	drop 删除数据库/表/视图 	create 创建数据库/表

```
查询权限
show grants for '用户名'@'主机名';
show grants for 'heima'@'%';
```

```
# 授予权限
grant 权限列表 ON 数据库名.表名 to '用户名'@'主机名';
grant all on itcast.emp to 'heima'@'%';
```

```
# 撤销权限
revoke 权限列表 ON 数据库名.表名 from '用户名'@'主机名';
revoke all on itcast.emp from 'heima'@'%';
```





### **第三章 函数**

#### **字符串函数**

concat(S1,S2,S3...Sn) 	字符串拼接

lower(str) 	转化成小写 

upper(str) 	转化成大写 

LPAD(str,n,pad)	左填充，用字符串pad对str的左边进行填充，达到n个字符串长度

RPAD(str,n,pad)	右填充

TRIM(str)		去除字符串头部和尾部空格

substring(str,start,len) 	返回字符串str从start位置起的len个长度的字符串

```
举例
select concat('hello','mysql');	 	hellomysql
select lower('Hello');     			hello
select upper('Hello');    		 	HELLO
select lpad('01',5,'-');    		---01
select rpad('01',5,'-');		    01---
select trim('    hello  mysql   ');         hello  mysql
select substring('hello MySql',1,3);        hel
```

#### **数值函数**

ceil(x) 	向上取整

floo(x)	向下取整

mod(x,y) 	取x/y的模

fand()		返回0~1的随机数

round(x,y)	求参数x的四舍五入的值，保留y位小数

```
# 生成一个六位数的随机验证码
select rpad(round(rand()*1000000,0),6,'0');
```

#### **日期函数**

```
curdate()							返回当前日期
curtime() 							返回当前时间		
now();								返回当前日期和时间
year(date)							获取指定data的年份
month(date)							获取指定date的月份
day(date)							获取指定date的日期
date_add(date,interval expr type); 	返回上一个日期/时间值加上一个时间间隔expr后的时间值
datediff(date1,date2);				返回起始时间date1和结束时间date2之间的天数
```

```
举例
select curdate();                               2025-06-15
select curtime();                               20:26:37
select now();                                   2025-06-15 20:26:49

select year(now());                             2025
select month(now());                            6

select date_add(now(),INTERVAL 1 year );        2026-06-15 20:28:53
select datediff(now(),'2005-10-13');            7185
```

#### **流程函数**    	实现条件筛选，提高效率

if(value,t,f)									如果value为true 则返回t，否则返回f

ifnull(value1,value2)						       如果value不为空 则返回value1，否则返回value2

case then [val1] then [res1]...else[default] end	  如果val1为true，返回res1,...否则返回默认值default

case [expr] when [val1] then [res1] ... else [default] end	如果expr等于val1，返回res1,...否则返回默认值default



### 第四章 约束

概述：作用于字段上的规则 用于限制存储在表中的数据

非空约束：not null

唯一约束：unique

主键约束：primary key 主键是一行数据的唯一标识，要求非空且唯一

默认约束：default 当保存数据时，未指定该字段的值，则采用默认值

检查约束：check 保证字段满足某一条件（8.0.16支持）

外键约束：foreign key 用来让两张表的数据建立连接，保证数据的一致性和完整性

**注意：约束是作用于表中字段上的，可以在建立表/修改表的时候添加约束**



#### **演示约束**

auto_increment	自动增长

```
create table user(
    id int primary key auto_increment comment '主键',
    name varchar(10) not null unique comment '姓名',
    age int check (age>0&&age<=120) comment '年龄',
    status char(1) default '1' comment '状态',
    gender char(1) comment '性别'
)comment '用户表';
```

#### 外键约束

让两张表的数据建立连接，保证数据的一致性和完整性

**建立外键**

```
create table 表名(
    字段名 数据类型,
    [constraint] [外键名称] foreign key(外键字段名) references 主表(主表列名)
)
```

```
alter table 表名 add constranint 外键名称 foreign key (外键字段名) reference 主表(主表列明);
```

**删除外键**

```
alter table 表名 drop foreign key 外键名称;
```

**外键的删除和更新行为**

no action /resrict 当父表中删除/更新对应记录时，首先检查是否有对应外键，如果有则不允许删除/更新。

cascade 当在父表中删除/更新对应记录时，首先检查记录是否对应外键，如果有，则删除/更新外键在子表中		的记录

set null 当在父表中删除/更新对应记录时，首先检查记录是否对应外键，如果有则设置子表中该外键值为		null(这就要求该外键允许取null)

set default 父表有变更时，子表将外键设置为一个默认的值（Innodb不支持）

```
alter table 表名 add constranint 外键名称 foreign key (外键字段名) reference 主表(主表列名) on update cascade on delete cascade;
```





### 第五章 多表查询

#### **多表关系**

一对多(多对一)	**在多的一方建立外键，指向一的一方的主键**

多对多		      **建立第三张中间表，中间表至少包含两个外键，分别关联两方主键**

一对一		      **在任意一方加入外键，关联另一方的主键，并且设置外键为唯一的（UNIQUE）**

#### **多表查询概述** 

笛卡尔积  两个集合，集合A和集合B的所有组合情况

在多表查询时，需要消除无效的笛卡尔积

多表查询分类

​	连接查询：

​		内连接：相当于查询A，B交集部分数据

​		外连接：左外连接  查询左表所有数据，以及两张表交集部分数据 

​				右外连接  查询右表所有数据，以及两张表交集部分数据 

​		自连接：当前表与自身的连接查询，自连接必须使用表别名

​	子查询：

##### **内连接**：

查询两张表交集部分 

1.隐式内连接

```
select 字段列表 from 表1，表2 where 条件 ...;
```

2.显式内连接

```
select 字段列表 from 表1 [inner] join 表2 on 连接条件 ...;
```

##### **外连接：**

1.左外 	相当于查询表1（左表）的所有数据 包含表1和表2交集部分的数据

```
select 字段列表 from 表1 left [outer] join 表2 on 条件...;
```

1.右外 	相当于查询表2（右表）的所有数据 包含表1和表2交集部分的数据

```
select 字段列表 from 表1 right [outer] join 表2 on 条件...;
```

##### **自连接：**

可以是内连接查询，也可以是外连接查询

```mysql
select 字段列表 from 表A 别名A join 表A 别名B on 条件...;
```

##### 联合查询：

```
select 字段列表 from 表A...
union [all]
select 字段列表 from 表B ...;
```

##### **子查询/嵌套查询：**

```
select * from t1 where column1 = (select column1 from t2);
```

子查询外部语句可以是select/insert/update/delete的任何一个

- 根据查询结构不同，可以分为：

​	1.标量子查询（子查询结果为单个值）

​	子查询返回结果是单个值（数字，字符串，日期），最简单的形式

​	常用的操作符：= 	<> 	> 	>= 	< 	<=

```
示例
select * from users where dept_id = (select id from dept where name = '市场部');
select * from users where id>(select id from users where name = 'Tom2');
```

​	2.列子查询（子查询结果为一列）

​	子查询的结果是一列（可以是多行）

​	常用操作符：in	not in	any	all

​	3.行子查询（子查询结果为一行）

​	常用操作符：=	<>	in	not in

​	4.表子查询（子查询结果为多行多列）

​	常用操作符：in

- 根据子查询位置，可以分为：where之后，from之后，select之后

##### **多表查询案例**



### 第六章 事务

##### 事务简介

操作的集合，不可分割单位，事务会将所有操作作为一个整体一起向系统提交或撤销的操作，即这些操作要么**同时成功，要么同时失败**。

##### 事务操作

- 查看/设置事务提交方式

```
select @@autocommit;
set @@autocommit = 0; 		1为自动提交 0为手动提交
```

- 提交事务

```
commit;
```

- 回滚事务

```
rollback;
```

- 开启事务

```
start transaction 或 begin
```

##### 事务四大特性

原子性：事务是不可分割的最小操作单元，要么全部失败，要么全部成功

一致性：事务完成时，必须使所有数据保持一致性

隔离性：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响地独立环境下进行

持久性：事务一旦提交或回滚，它对数据库的数据的改变是永久的

##### 并发事务问题

脏读：一个事务读到另一个事务还没有提交的数据。

不可重复读：一个事务先后读取同一条记录，但是两次读取数据不同，称为不可重复读。

幻读：一个事务按照条件查询数据时，没有对应数据行，但是在插入数据时，又发现这行数据已存在，好像1出现了”幻影“。

##### 事务间隔级别

​									脏读				不可重复			  幻读		性能		  安全性

read uncommitted(读未提交)			√ 					√				   √		     高			低

read committed（读可提交）		       × 					√				   √

repeatable read(默认 可重复读)		    ×					 ×   				√

serializable(串行化)				         ×					 ×				    ×		    低			 高



查看事务隔离级别

```
select @@transaction_isolation;
```

设置事务隔离级别

```
set [session | global] transaction isolation level {read uncommitted | read committed | repeatable read | serializable}
```







