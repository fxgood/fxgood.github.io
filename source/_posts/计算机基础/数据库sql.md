---
title: MySQL
category: 计算机基础
---

对数据库基本原理和sql相关内容进行复习总结。

<!-- more -->

mysql登录方法：

1. windows cmd打开
2. mysql -u root -p
3. 输入密码123456

# 概述

将MySQL的学习分为三个部分：

1. 基础
   - SQL
   - 函数
   - 约束
   - 多表查询
   - 事务
2. 进阶
   - 存储引擎
   - 索引
   - SQL优化
   - 视图/存储过程/触发器
   - 锁
   - InnoDB核心
   - MySQL管理
3. 运维
   - 日志
   - 主从复制
   - 分库分表
   - 读写分离

# SQL语法

| 分类 | 全程                       | 说明                                                   |
| ---- | -------------------------- | ------------------------------------------------------ |
| DDL  | Data Definition Language   | 数据定义语言，用来定义数据库对象（数据库，表，字段）   |
| DML  | Data Manipulation Language | 数据操作语言，用来对数据库表中的数据进行增删改         |
| DQL  | Data Query Language        | 数据查询语言，用来查询数据库中表的记录                 |
| DCL  | Data Control Language      | 数据控制语言，用来创建数据库用户、控制数据库的访问权限 |

## DDL

### 数据库操作

1. 查询
   - 查询所有数据库 `	SHOW DATABASES;`
   - 查询当前数据库`SELECT DATABASE();`
2. 创建
   - `CREATE DATABASE [IF NOT EXISTS] 数据库名 [default charset 字符集] [collate 排序规则]`
3. 删除
   - `DROP DATABASE [IF EXISTS] 数据库名；`
4. 使用
   - `USE 数据库名;`

### 表操作

1. 查询
   - 查询当前数据库所有表 `SHOW TABLES;`
   - 查询表结构 `DESC 表名;`
   - 查询指定表的建表语句 `SHOW CREATE TABLE 表名;`

2. 创建

   ![image-20220223104104030](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220223104104030.png)

MySQL数据类型

**数值类型**

![image-20220223110037817](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220223110037817.png)

 描述只有一位小数的考试分数`score double(4,1)` 表示这个数字一共4位，其中小数占1位，整数3位。

**字符串类型**

![image-20220223110353590](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220223110353590.png)

**日期时间类型**

![image-20220223110709371](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220223110709371.png)

3. 修改表
   - 添加字段	`alter table 表名 add 字段名 类型(长度) [COMMENT] [约束];`
   - 修改数据类型      `alter table 表名 modify 字段名 新数据类型(长度);`
   - 修改字段名和字段类型     `alter table 表名 change 旧字段名 新字段名 类型(长度) [comment][约束]`;
   - 删除字段   `alter table 表名 drop 字段名;`
   - 修改表名    `alter table 表名 rename to 新表名;`
   - 删除表     `drop table [if exists] 表名`
   - 删除指定表，并重新创建该表    `truncate table 表名;`

## DML

### 添加数据

1. 给指定字段添加数据	`insert into 表名(字段名1，字段名2,...) values(值1,值2,值3...);`
2. 给全部字段添加数据   `insert into 表名 values(值1，值2，...);`
3. 批量添加数据
   - `insert into 表名(字段名1，字段名2,...) values(值1,值2,值3...),(值1，值2..),(值1，值2..);`
   - `insert into 表名 values(值1，值2,...),(值1，值2,...),(值1，值2,...);`

>  注：字符串和日期类型数据需要包含在引号中；插入的数据大小应该在字段的规定范围内。

### 修改数据

`update 表名 set 字段名1=值1, 字段名2=值2,...[where 条件];`

### 删除数据

`delete from 表名 [where 条件]`;

## DQL

正常业务系统中，查询的频率远高于增删改。

DQL语法：

```sql
select 		字段列表
from		表名列表
where		条件列表
group by	分组字段列表
having		分组后条件列表
order by 	排序字段列表
limit		分页参数
```

### 基本查询

1. 查询多个字段 
   - `select 字段1，字段2... from 表名`;
   - `select*from 表名;`
2. 设置别名
   - `select 字段1[as 别名1], 字段2[as 别名2]... from 表名;`  # as可以省略
3. 去除重复记录
   - `select distinct 字段列表 from 表名`;

### 条件查询

语法：`select 字段列表 from 表名 where 条件列表;`

条件：

![image-20220223142837718](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220223142837718.png)

### 聚合函数

聚合函数：将**一列数据**作为整体进行纵向计算

> 注意：所有NULL值不参与聚合运算，比如不算在count内

count、max、min、avg、sum

### 分组查询 group by

where和having区别

1. 执行时机不同：where是分组前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤
2. 判断条件不同：where不能对聚合函数进行判断，而having可以

注意：

1. 执行顺序：where>聚合函数>having
2. 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

### 排序查询 order by

语法：select 字段列表 from 表名 order by 字段1 排序方式1，字段2 排序方式2……;

(如果是多个字段排序，当第一个字段值相同时，才会根据第二个字段进行排序)

### 分页查询 limit

select 字段列表 from 表名 limit 起始索引,查询记录数；

注意：

1. 起始索引从0开始，起始索引=(查询页码-1)*每页的记录数。
2. 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是limit
3. 如果查询的是第一页数据，起始索引可以省略，直接简写成limit 10；



### DQL语句执行顺序

执行顺序非编写顺序！

![image-20220305105604648](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220305105604648.png)

## DCL

DCL英文全称Data Control Language 数据控制语言，用来管理数据库用户、控制数据库的访问权限。

### 用户管理

1. 查询用户
   - use mysql;
   - select*from user;
2. 创建用户
   - create user '用户名'@'主机名' identified by '密码';
3. 修改用户密码
   - alter user '用户名'@'主机名' identified with mysql_native_password by '新密码';
4. 删除用户
   - drop user '用户名'@主机名;

主机地址+用户名，才能唯一定位一个mysql用户，对应用户名只能在特定主机地址上访问mysql

### 权限控制

常用权限如下

![image-20220305110915070](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220305110915070.png)

1. 查询权限
   - show grants for '用户名'@'主机名';
2. 授予权限
   - grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
3. 撤销权限
   - revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';

# 函数

# 约束

## 概述

1. 概念
   - 约束是作用于表中字段上的规则，用于限制存储在表中的数据。
2. 目的
   - 保证表中数据的正确性、完整性、一致性
3. 分类

| 约束                     | 描述                                                     | 关键字      |
| ------------------------ | -------------------------------------------------------- | ----------- |
| 非空约束                 | 限制该字段数据不能为null                                 | NOT NULL    |
| 唯一约束                 | 保证该字段所有数据唯一、不重复                           | UNIQUE      |
| 主键约束                 | 主键是一行数据的唯一标识，非空且唯一                     | PRIMARY KEY |
| 默认约束                 | 保存数据时，如果未指定该字段的数据，则采用默认值         | DEFAULT‘    |
| 外键约束                 | 用来在两张表的数据之间建立联系，保证数据的一致性和完整性 | FOREIGN KEY |
| 检查约束(8.0.16版本之后) | 保证字段值满足某些条件                                   | CHECK       |

> 完整性的定义：它是应防止数据库中存在不符合语义规定的数据和防止因错误信息的输入输出造成无效操作或错误信息而提出的。
>
> 一致性：我的理解:如果a表的外键关联着b表，那么b表中的数据不能随意删除，否则会导致a表的外键引用出错

自动增长关键字：AUTO_INCREMENT

案例

![image-20220305201536133](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220305201536133.png)

## 外键约束

概念：对两张表的数据进行联系，以保证数据的完整性、一致性。

![image-20220305204334339](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220305204334339.png)

增加外键的两种方式

1.创建表时添加

![image-20220305204753491](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220305204753491.png)

2.修改表

![image-20220305204827056](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220305204827056.png)

删除外键

alter table drop foreign key 外键名称;

## 外键约束中的行为

删除/更新行为

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| not action  | 当在父表中更新/删除对应记录时，首先检查该记录是否有对应的外键，如果有则不允许删除/更新（与restrict一致） |
| restrict    | 当在父表中更新/删除对应记录时，首先检查该记录是否有对应的外键，如果有则不允许删除/更新（与not actiont一致） |
| cascade     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。 |
| set null    | 当在父表中删除对应记录时，首先应检查是否有对应外键，若有，则设置子表中该外键的值为null（这里要求该外键允许取null） |
| set default | 父表有变更时，子表将外键列设置为默认值（innodb不支持）       |

alter table 表名  add constraint 外键名称 foreign key(外键字段) references 主表名(主表字段名) on upadate cascade on delete cascade;

# 多表查询

## 多表关系

1.一对多关系

- 案例：部门与员工的关系
- 关系：一个部门对应多个员工，一个员工对应一个各部门
- 实现：在多的一方建立外键，指向一的一方的主键

![image-20220306094246376](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220306094246376.png)

## 多表查询概述

直接查询两张表的数据，出来的是笛卡尔积

例如：select*from emp,dept; 

笛卡尔积：笛卡尔乘积在数学中，两个集合A集合和B集合的所有组合情况。（在多表查询时，需要消除无用的笛卡尔积）

## 内连接

内连接：相当于查询A、B交集的部分

1. 隐式内连接
   - `select 字段列表 表1，表2  where 条件`
2. 显式内连接
   - select 字段列表 from 表1 [inner] jion 表2 on连接接条件...;

## 外连接

左外连接：查询**左表**所有数据，以及两张表交集部分的数据

右外连接：查询**右表**所有数据，以及两张表交集部分的数据

1. 左外连接
   - select 字段列表 from 表1 left [outer] join 表2 on 条件...;
2. 右外连接
   - select 字段列表 from 表1 right [outer] jion 表2 on 条件...；  
   - 可以通过左外连接写出右外连接的效果（交换两个表的位置即可）

## 自连接

自连接：当前表与自身的连接查询，自连接必须使用表别名

语法“ select 字段列表 from 表a 别名a join 表a 别名b on 条件....;

自然连接查询，可以是内连接查询，也可以是外连接查询。

案例一：

查询员工及其所属领导的名字

select a.name ,b.name from emp a,emp b where a.manager =b.id;

案例二：

查询所有员工emp及其领导的名字，如果员工没有领导，也需要查询出来

select a.name, b.name from emp a left outer join emp b on a.managerid=b.id;

## 自然连接

```sql
select * from dept natural join emp;
select * from dept natural left join emp;
```

## 联合查询

对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

```sql
select 字段列表 from 表a ...
uion [all]
select 字段列表 from 表b....
```

**注意：**

**1.uoin all会将全部的数据直接合并在一起，union则会对合并后的数据去重**

**2.使用联合查询的条件：对于联合查询的多张表列数必须保持一致，字段的类型也需要保持一致**

## 子查询

概念：sql语句中嵌套select语句，称为**嵌套查询**，又称为**子查询**、

select * from t1 where col1=(select col1 from t2); 

子查询的外部语句可以是insert/update/delete/select的任何一个

根据子查询的结果不同，分为：

- 标量子查询（子查询的结果为一个）
- 列子查询（子查询的结果为一列）
- 行子查询（子查询的结果为一行）
- 表子查询（子查询的结果为多行多列）

根据子查询的位置，分为where之后，from之后，select之后 

### 标量子查询

查询在“方东白”入职之后的员工信息

`select * from emp where entrydate > (select entrydate from emp where name = '方东白');`

### 列子查询

子查询的结果为一列(可以是多行)

常用的操作符有：IN, NOT IN, ANY, SOME, ALL

| 操作符 | 描述                                   |
| ------ | -------------------------------------- |
| IN     | 在指定的集合范围内，多选一             |
| NOT IN | 不在指定的集合范围内                   |
| ANY    | 子查询返回列表中，有任意一个满足即可   |
| SOME   | 与ANY等同，使用SOME的地方都可以使用ANY |
| ALL    | 子查询返回列表的所有值都必须满足       |

![image-20220308112709524](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220308112709524.png)

![image-20220308112728649](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220308112728649.png)

### 行子查询

行子查询返回的结果是一行（可以是多列），这种子查询称为行子查询

常用的操作符：=、<>、IN、NOT IN

![image-20220308143101728](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220308143101728.png)

### 表子查询

子查询返回的结果是多行多列，这种子查询称为表子查询。

常用操作符：IN

![image-20220308143939209](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220308143939209.png)

## 多表查询案例

略

# 事务



> 学习内容来自 https://www.bilibili.com/video/BV1Kr4y1i7ru?p=49&spm_id_from=pageDriver
