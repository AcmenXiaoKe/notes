# Mysql

> 默认端口：3306
>
> 注意 mysql 命令 的需要 ; 来结束

### Mysql数据库中最基本的单元是表：table

什么是table？为什么用表来存储数据？

| 姓名 | 性别 | 年龄 |
| ---- | ---- | ---- |
| 小白 | 女   | 12   |
| 小强 | 男   | 18   |
| 小胖 | 男   | 15   |

列：字段

-------> 行记录

Mysql数据库中是以表的形式表示数据的

因为表比较直观

如何一张表都有行和列：

​	行（row）：被称为数据/记录

​	列（column)：被称为字段

每一个字段都有：字段名，数据。类型，约束等属性

## SQL分类

> SOL语句有很多，最好进行分门别类，这样子更容易记忆

- DQL：数据查询语言（凡是带有select关键字的都是查询语句）

  - select ...

- DML：数据操作语言（凡是对表当中的数据进行增删改的都是DML）

  - insert 增
  - delete 删
  - update 改

- DDL：数据定义语言（凡是带有 create  drop alter 的都是DDL ）DDL主要操作的是表的结构，不是表中的数据

  - create  新建，等同于增

  - drop 删除

  - alter 修改

    > 这个增删改和DML不同，这个主要是对表的结构进行操作

- TCL：事物控制语言 

  - 事物提交：commit;
  - 事物回滚：rollback;

- DCT：数据控制语言

  - 授权 grant
  - 撤销权限 revoke  等等...



## 基本命令

```mysql
登陆：bin/mysql -uroot -p密码  显示密码
```

```mysql
登陆：bin/mysql -uroot -p  隐藏密码
```

```mysql
查看全部数据库 show databases;
```

```mysql
切换数据库 use 数据库名;
```

```mysql
查看mysql版本号  select version();
```

```mysql
查看当前数据库 select database();
```

```mysql
创建数据库 create database 数据库名;
```

```mysql
查看当前数据库的表 show tables;
```

```mysql
数据库导入 source 路径;
```

```mysql
查询表的数据 select * from 表名;
```

```mysql
查询表的结构 desc 表名;
```

```mysql
查看创建表的时候的sql语句 ： show create table 表名
```

## DQL语句

### 简单查询

1. 查询一个字段（ 查询多个字段使用 , 来隔开 ）

   ```mysql
   select 字段名 from 表名;
   查询的时候 可以在字段名后面添加数学运算
   比如说:
   select ENAME,SAL*12 as '年薪' from emp; 给SAL起'年薪'这个别名
   ```

2. 查询所有字段

   ```mysql
   select * from 表名;
   ```

3. 查询字段起别名

   ```mysql
   select 字段名 '别名' from 表名;
   ```

### 条件查询

> 条件查询需要用到 where 语句，where 必须放到 from 语句表的后面 

支持如下运算符

| 运算符          | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| =               | 等于                                                         |
| <>或!=          | 不等于                                                       |
| <               | 小于                                                         |
| <=              | 小于等于                                                     |
| >               | 大于                                                         |
| \>=             | 大于等于                                                     |
| between …and …. | 两个值之间,等同于 >= and <=   必须左小右大                   |
| is null         | 为 null（is not null 不为空）                                |
| and             | 并且                                                         |
| or              | 或者 如果 and 和 or 同时出现  and的优先级高 如果给 or 语句加括号 就 or的语句 优先级高                                              比如：select * from emp where SAL > 2500 and (DEPTNO = 10 or DEPTNO =  20); |
| in              | 包含，相当于多个 or（not in 不在这个范围中）例如：select * from emp  where JOB in ('MANAGER','SALESMAN'); |
| not             | not 可以取非，主要用在 is 或 in 中                           |
| like            | like 称为模糊查询，支持%或下划线匹配 %匹配任意个字符 下划线，一个下划线只匹配一个字符   如果需要查询的内容有特殊字符比如说： _  需要使用 \ 这个转义符 进行 转换                                                                                                            例如：select * from emp where ENAME like '%O%';   (含有 0 )   select * from emp where ENAME like '%T';  （以T结尾）select * from emp where ENAME like 'J%';  ( 以J开始的) select * from emp where ENAME like '_A%'; （第二个字母是A） |

##### 基本语法

```mysql
select 字段 from 表名 where 条件;
例如：select ENAME,SAL  from emp where SAL>4000;
```

### 排序数据

升序

```mysql
select 字段名 from 表名 order by asc;  如果是升序 asc 可以不写 因为默认就是 升序
```

降序

```mysql
select 字段名 from 表名 order by 字段名 desc;
```

### 数据处理函数/单行处理函数

> 数据处理函数又称为单行处理函数
>
> 单行处理函数的特点：一个输入对应一个输出 
>
> 和单行处理函数相对的是：多行处理函数（多个输入，对应一个输出）

| lower       | 转换小写  例如  select lower(ename) from emp;                |
| ----------- | ------------------------------------------------------------ |
| upper       | 转换大写                                                     |
| substr      | 取子串（substr(被截取的字符串,起始下标, 截取的长度)） 例如：select substr(ename,1 ,3) as name  from emp; |
| length      | 取长度 例如：select length(sal) from emp;                    |
| trim        | 去空格 例如：select * from emp where ename =trim( '           WARD'); |
| str_to_date | 将字符串转换成日期                                           |
| date_format | 格式化日期                                                   |
| format      | 设置千分位                                                   |
| round       | 四舍五入                                                     |
| rand()      | 生成随机数select round(rand()*10,0) as num;   //10以内的随机数 |
| Ifnull      | 可以将 null 转换成一个具体值                                 |
| concat      | 函数进行字符串的拼接 例如：select ifnull(COMM,0) from emp;  如果为null 替换为 0 |

### 分组函数/聚合函数/多行处理函数

> 输入多行，输出一行

注意：分组函数 自动忽略 null 不用提前处理 分组函数不能**直接**使用在 **where** 语句中

分组函数在使用的时候必须进行分组，然后才能使用，然后你没有对数据进行分组，整张表默认为一组

分组函数可以组合一起使用 比如：select min(sal),max(sal),avg(sal),sum(sal),count(*) from emp;

| count | 取得记录数 |
| ----- | ---------- |
| sum   | 求和       |
| avg   | 取平均     |
| max   | 取最大的数 |
| min   | 取最小的数 |

### 分组查询

> 分组查询主要涉及到两个子句，分别是：group by 和 having
>
> 在实际应用中，我们可能有这样的需要，需要先进行分组，然后对每一组的数据进行操作。
>
> 

### 连接查询

> 连接查询就是多张表来查询

分类

- 内连接
  1. 等值连接
  2. 非等值连接
  3. 自连接
- 外连接
  1. 左边连接 （左连接）
  2. 右边连接 （右连接）
- 全连接

基本语法

```mysql
select 字段,字段 from 表名,表名;
```

### union 将查询结果合并

```mysql
select ... from emp
union
select ... from emp;
```

### limit 分页

```mysql
select 字段名 from 表名 limit 跳过的条数,显示条数;
分页公式：
	每页显示pageSize条记录
		第pageNo页：limit (pageNo-1) * pageSize
```



### 执行顺序

```mysql
select 
	...
from
	...
where
	...
group by
	...
having
	....
order by
	...
limit
	...


以上执行顺序不能颠倒，需要记忆
执行顺序
	1. from
	2. where
	3. group by
	4. having
	5. select
	6. order by
	7. limit
```

## DDL语句

### 数据类型

- varchar  可变长度的字符串  比较智能 节省空间 会根据实际的数据动态分配空间 （最长255）
- char 定长字符串 不管实际的数据的长度 分配 固定的数据空间 （最长255）
- int 整数 （最长11）
- bigint 长整数 
- float 单精度浮点型
- double 双精度浮点型
- date 短日期
- datetime 长日期
- clob 字符大对象 最多可以存储 4G 的字符串 存储 文章 （成过255）
- blob 二进制 大对象  存储 图片 声音 视频 等流媒体 例如：插入一个视频，图片 等 你需要 IO 流 

### 约束

> 约束 constraint  在创建表的时候，我们可以给表的字段加上一些约束，来保证表中数据的完整性、有效性！
>
> 作用：就是保证表中的数据有效！！

### 约束包括

- 非空约束：not null
- 唯一性约束：unique
- 主键约束：primary key (简称 PK)
- 外键约束：foreign key 简称（FK)
- 检查约束：check ( mysql 不支持  oracle 支持 )

```mysql
创建表的时候条件约束
例如：create table 表名 ( 字段名 数据类型 default 默认值 约束的类型 , 字段名 数据类型 ... )
```

### 主键约束

> 在mysql中 有一种机制，可以帮助我们自动维护一个主键值 auto_increment
>
> 自增 从 1 开始 以 1 自增
>
> 例如下：

```mysql
create table user(
	id int primary key auto_increment,
	name varchar(255),
    password  varchar(255)
);
insert into t_vip(name) values('xiaoke');
insert into t_vip(name) values('xiaoke');
insert into t_vip(name) values('xiaoke');
insert into t_vip(name) values('xiaoke');
insert into t_vip(name) values('xiaoke');
insert into t_vip(name) values('xiaoke');
```

### 外键约束

> 约束原理和 主键约束 大概  一样	
>
> 实例：本地的	test  里面的   父表 t_class 子表 t_student

```mysql
drop table if exists t_student; //  这个是 子表
drop table if exists t_class;   // 	这个是父表	先创建 父表 再 创建子表 
create table t_class(
	classno int primary key,
	classname varchar(45)
);
create table t_student(
	no int primary key auto_increment,
	name varchar(45),
	con int,
	foreign key(需要外键约束的字段名) references 外键表名(外键 字段名)
)

create table dome  (
	id int primary key auto_increment,
	img_name varchar(255),
    img_url  varchar(255),
    width  varchar(255),
    height  varchar(255)
)

```



### 创建表

```mysql
create table 表名 ( 字段名 数据类型 default 默认值 , 字段名 数据类型 ... )
```

### 删除表

```mysql
drop table 表名; //当一张表不存在的时候会报错
drop table if exists 表名; // 如果这张表存在就删除
```

## DML语句

### 插入数据

```mysql
insert into 表 (字段名1,字段名2,字段名3) values(值1,值2,值3),(值1,值2,值3),(值1,值2,值3); // 要 一一对应 插入多条数据用 , 来隔开
例如：insert into 表名 (no,name,sex,age,email) values(1,'xiaoke','m',15,'xiaoke@88.com');
```

> 注意：insert 语句执行成功 那么必须多一条数据
>
> 没有给其他字段指定值得话，默认值是 null

### 修改数据

```mysql
update 表名 set 字段名1 = 值1 , 字段名2 = 值2, 字段名3 = 值3 where 条件;
例如：update t_user set sex = 'f' where no = 2;
注意：没有条件限制会导致所有数据全部更新
```

### 删除数据

```mysql
delete from 表名 where 条件;
注意：没有条件，整一张表都会被删除
```

## 事物 

### 什么是事物？

事物： transaction

一个事物其实就是一个完整的业务逻辑

事物就是多个 DML 语句 同时 成功 或 同时 失败

### 什么是一个完整的逻辑？

假设转账，从A账户向B账户中转账10000

将A账户的钱 -10000 （ update ）

将B账户的钱 +10000 （ updata ）

这是一个完整的业务逻辑

以上操作是一个最小的工作单元，要么同时成功要么同时失败，不可再分

这两个 updata 语句 要求必须同时成功 或 同时 失败 ，这样才能保证钱是正确的

### 事物的关系

只有 DML 语句 和事物 有关系 其他都和事物 没关系

insert    delete  update   只有这三个语句 和 事物 有关系 其他的 都没有关系

### 提交事物

> 清空事物性活动的日志文件，将数据全部彻底持久化到数据库表中
>
> 提交事物标志着，事物的结束，并且是一种全部成功的结束
>
> 在mysql中 默认事物行为是自动提交事物
>
> 如何关闭自动提交机制： start transaction;

提交事物 commit;



### 回滚事物

> 将之前所有的DML操作全部撤销，并且清空事物性活动的日志文件
>
> 回滚事物标志着，事物的结束，并且是全部失败的结束
>
> 回滚只能回滚到上一次提交点

回滚事物 rollback;

### 事物包括4个特性

- A ：原子性

  说明事物是最小的工作单元，不可再分

- C ：一致性

  所有事物要求，在同一个事物当中，所有操作必须同时成功，或者同时失败

- I ：隔离性

  A 事物 和 B 事物之间具有一定的隔离

- D ：持久性

  事物最终结束的一个保障，事物提交，就想当与没有保存到硬盘的数据保存到硬盘上。

#### 重要的是事物的隔离性

A教室和B教室中间有一道墙，这道墙可以很厚，也可以很薄，这就是事物的隔离级别，这道墙越厚，表示隔离级别越高

##### 事物和事物之间的隔离级别有4个级别

- 读未提交：read uncommitted       （最低的隔离级别）

  事物A可以读取到事物B未提交的数据

  这种隔离级别存在的问题就是：脏读现象 (Dirty Read) 我们称读到了脏数据

- 读已提交：read committed

  事物A只能读取到事物B提交之后的数据 这种隔离级别

   解决了：脏读的现象

   存在问题：不可重复读取数据

  什么是不可重复读取数据？

  在事物结束后，第一次读到的数据是3条，当前事物还没有结束，可能第二次再读取的时候，读取到的数据是4条，3不等于4称为不可重复读取

  这种隔离级别是比较真实的数据，每一次的数据是绝对的真实

  oracle 数据库 默认的隔离级别就是：read committed

- 可重复读：repeatable read

  事物A开启之后，不管是多久，每一次在事物A中读取到的数据都是一致的，即使事物B将数据已经修改，并且提交，事物A读取到的数据还是没有发生改变，这就是可重复读取

  可重复读解决了不可重复读

  可重复读的缺点：每一次读到的数据都是幻象，不够真实

- 序列化：serializable        （最高的隔离级别）

  这是最高隔离级别，效率最低，解决了所有问题
  
  这种隔离级别表示事物排队，不能并非
  
  每一次读取到的数据最真实

## 索引

### 什么是索引

> 索引是在数据库表的字段是添加，是为了提高查询效率存在的一种机制
>
> 一张表的一个字段可以添加一个索引，当然多个字段联合起来也可以添加索引
>
> 索引相当与一本书的目录，是为了缩小扫描范围而存在的一种机制
>
> mysql 在查询方面主要就是两种方式：
> 								第一种方式：全表扫描
>
> ​								第二种方式：根据索引检索
>
> 注意：在实际中，汉语字典前面的目录是排序的，按照 a b c d e f .... 排序，为什么排序呢？因为只有排序了才会有区间查询这一说 （缩小扫描范围其实就是扫描某个区间）
>
> 在mysql数据库当中索引也是需要排序的
>
> 遵循左小右大原则存放，采用中序遍历方式遍历取数据

### 索引的实现原理？

假设有一张用户表：t_user

id(PK)					name						每一行记录在硬盘上都有物理存储编号

100						zhangsan						 0x1111
120						lisi									0x2222
99							wangwu						 0x8888
88							zhaoliu						   0x9999
101						jack								  0x6666
55							lucy						        0x5555
130						tom						          0x7777

提醒1：在任何数据库当中主键上都会自动添加索引对象，id字段上自动有索引，
因为id是PK。另外在mysql当中，一个字段上如果有unique约束的话，也会自动
创建索引对象。

提醒2：在任何数据库当中，任何一张表的任何一条记录在硬盘存储上都有
一个硬盘的物理存储编号。

提醒3：在mysql当中，索引是一个单独的对象，不同的存储引擎以不同的形式
存在，在MyISAM存储引擎中，索引存储在一个.MYI文件中。在InnoDB存储引擎中
索引存储在一个逻辑名称叫做tablespace的当中。在MEMORY存储引擎当中索引
被存储在内存当中。不管索引存储在哪里，索引在mysql当中都是一个树的形式
存在。（自平衡二叉树：B-Tree）

在mysql当中主键上，以级unique字段上会自动添加索引

### 什么条件下考虑给字段添加索引呢？

​	条件一：数据量庞大（到底多少算庞大，这个需要测试，因为每一个硬件环境不同）

​	条件二：该字段经常出现在 where 的后面以条件的形式的存在，也就是说该字段经常被扫描

​	条件三：该字段很少的 DML 操作 （因为DML之后，索引需要重新排序）	

​	建议不要随意添加索引，因为索引也是需要维护的，太多的话反而会降低系统的性能

​	建议通过主键查询，建议通过unique约束字段进行查询，效率是比较高的

### 创建索引

```mysql
create index 索引名称(自定义) on 表名(字段名); 
```

删除索引

```mysql
drop index 索引名称 on 表名;
```

在mysql当中，怎么查看SQL语句是否使用了索引进行检索？
explain SQL语句

### 索引失效

1. 

```mysql
select * from emp where ename like '%T';

ename上即使添加了索引，也不会走索引，为什么？
原因是因为模糊匹配当中以 % 开头了
尽量避免模糊匹配查询的时候以 % 开启
```

2. 

使用 or 的时候会失效，如果使用 or 那么要求两边的条件字段都要有索引，才会走索引，如果其中一边有一个字段没有索引，那么另一个字段上的索引以会失效，尽量少用 or 

3. 在 where 当中 索引列 参加了运算 索引失效

   例如：select * from emp where sal+1 = 800;

4. 在 where 当中 索引列 条件函数 索引失效

   例如：select * from emp where lower(name) = 800;

## DBA

### 创建用户

```mysql
create user 用户名称 identified by '密码'
```

### 导出导入

#### 导出整个数据库

```mysql
在 windows 的 dos 命令窗口中执行：mysqldump 数据库名>D:\bjpowernode.sql -uroot -p123
```

#### 导出指定库下的指定表

```mysql
在 windows 的 dos 命令窗口中执行：mysqldump 数据库名 字段名> D:\ bjpowernode.sql -uroot –p123
```

#### 导入

```mysql
登录 MYSQL 数据库管理系统之后执行：source D:\ bjpowernode.sql
```

