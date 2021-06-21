### 30.使用子查询的方式找出属于Action分类的所有电影对应的title,description

film表 

| 字段        | 说明         |
| ----------- | ------------ |
| film_id     | 电影id       |
| title       | 电影名称     |
| description | 电影描述信息 |

```sql
CREATE TABLE IF NOT EXISTS film (
film_id smallint(5)  NOT NULL DEFAULT '0',
title varchar(255) NOT NULL,
description text,
PRIMARY KEY (film_id));
```

category表 

| 字段        | 说明                 |
| ----------- | -------------------- |
| category_id | 电影分类id           |
| name        | 电影分类名称         |
| last_update | 电影分类最后更新时间 |

```sql
CREATE TABLE category  (
category_id  tinyint(3)  NOT NULL ,
name  varchar(25) NOT NULL, `last_update` timestamp,
PRIMARY KEY ( category_id ));
```

film_category表 

| 字段        | 说明                                 |
| ----------- | ------------------------------------ |
| film_id     | 电影id                               |
| category_id | 电影分类id                           |
| last_update | 电影id和分类id对应关系的最后更新时间 |

```sql
CREATE TABLE film_category  (
film_id  smallint(5)  NOT NULL,
category_id  tinyint(3)  NOT NULL, `last_update` timestamp);
```

你能使用子查询的方式找出属于Action分类的所有电影对应的title,description吗 

输入如：

```sql
INSERT INTO film VALUES(1,'ACADEMY DINOSAUR','A Epic Drama of a Feminist And a Mad Scientist who must Battle a Teacher in The Canadian Rockies');
INSERT INTO film VALUES(2,'ACE GOLDFINGER','A Astounding Epistle of a Database Administrator And a Explorer who must Find a Car in Ancient China');
INSERT INTO film VALUES(3,'ADAPTATION HOLES','A Astounding Reflection of a Lumberjack And a Car who must Sink a Lumberjack in A Baloon Factory');

INSERT INTO category VALUES(1,'Action','2006-02-14 20:46:27');
INSERT INTO category VALUES(2,'Animation','2006-02-14 20:46:27');
INSERT INTO category VALUES(3,'Children','2006-02-14 20:46:27');
INSERT INTO category VALUES(4,'Classics','2006-02-14 20:46:27');
INSERT INTO category VALUES(5,'Comedy','2006-02-14 20:46:27');
INSERT INTO category VALUES(6,'Documentary','2006-02-14 20:46:27');

INSERT INTO film_category VALUES(1,1,'2006-02-14 21:07:09');
INSERT INTO film_category VALUES(2,1,'2006-02-14 21:07:09');
INSERT INTO film_category VALUES(3,6,'2006-02-14 21:07:09');
```

输出： 

| title            | description                                                  |
| ---------------- | ------------------------------------------------------------ |
| ACADEMY DINOSAUR | A Epic Drama of a Feminist And a Mad Scientist who must Battle a Teacher in The Canadian Rockies |
| ACE GOLDFINGER   | A Astounding Epistle of a Database Administrator And a Explorer who must Find a Car in Ancient China |

`Answer`

```sql
select a.title,a.description
from film a where a.film_id in 
(
select 
b.film_id
from film_category b
where b.category_id in
(
select a.category_id from category a where a.name = 'Action'
)
)
;
```





### 32. 将employees表的所有员工的last_name和first_name拼接起来作为Name，中间以一个空格区分

题目描述

将employees表的所有员工的last_name和first_name拼接起来作为Name，中间以一个空格区分

(注：sqllite,字符串拼接为 || 符号，不支持concat函数，mysql支持concat函数)

```sql
`CREATE TABLE `employees` ( `emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

## 输入描述:

```
无
```

## 输出描述:

```
Name

				Facello Georgi
			

				Simmel Bezalel
			

				Bamford Parto
			

				Koblick Chirstian
			

				Maliniak Kyoichi
			

				Preusig Anneke
			

				Zielinski Tzvetan
			

				Kalloufi Saniya
			

				Peac Sumant
			

				Piveteau Duangkaew
			

				Sluis Mary
			
```

示例1

## 输入

[复制](javascript:void(0);)

```
无
```

## 输出

[复制](javascript:void(0);)

```
无
```

==MySQL、SQL Server、Oracle等数据库支持CONCAT方法，
而本题所用的SQLite数据库只支持用连接符号"||"来连接字符串== 

CONCAT方法：

```sql
select CONCAT(CONCAT(last_name," "),first_name) as name  from employees;
```

或者

```sql
select CONCAT(last_name," "，first_name) as name  from employees;
```



本题中使用：

```sql
select last_name||" "||first_name as name  from employees;
```

### 33. 创建一个actor表，包含如下列信息

创建一个actor表，包含如下列信息

| 列表        | 类型        | 是否为NULL | 含义   |
| :---------- | :---------- | :--------- | :----- |
| actor_id    | smallint(5) | not null   | 主键id |
| first_name  | varchar(45) | not null   | 名字   |
| last_name   | varchar(45) | not null   | 姓氏   |
| last_update | date        | not null   | 日期   |



```sql
#思路：在基本创建表的sql语句上注意两点：
#（1）设置主键primary key
#（2）设置默认时间DEFAULT (datetime('now','localtime'))
#【我是设置主键的单词没记住，不会设置默认时间，特此记录一下】

create table actor(
   actor_id smallint(5) not null primary key,
   first_name varchar(45)  not null ,
   last_name varchar(45)  not null ,
   last_update timestamp not null DEFAULT (datetime('now','localtime'))
)
;


create table if not exists actor (
    actor_id smallint(5) not null primary key COMMENT'主键id',
    first_name varchar(45) not null COMMENT'名字',
    last_name varchar(45) not null COMMENT '姓氏',
    last_update date not null COMMENT '日期' 
)
```



### 34.批量插入数据

题目描述

题目已经先执行了如下语句:

```sql
drop table if exists actor;
CREATE TABLE actor (
   actor_id  smallint(5)  NOT NULL PRIMARY KEY,
   first_name  varchar(45) NOT NULL,
   last_name  varchar(45) NOT NULL,
   last_update  DATETIME NOT NULL);
```

请你对于表actor批量插入如下数据(不能有2条insert语句哦!)

| actor_id | first_name | last_name | last_update         |
| :------- | :--------- | :-------- | :------------------ |
| 1        | PENELOPE   | GUINESS   | 2006-02-15 12:34:33 |
| 2        | NICK       | WAHLBERG  | 2006-02-15 12:34:33 |

示例1

## 输入

[复制](javascript:void(0);)

```
无
```

## 输出

[复制](javascript:void(0);)

```
无
```

本题的批量插入数据要求在一条语句内完成，以下有两种方法供参考：

方法一：利用VALUES(value1, value2, ...), (value1, value2, ...),
...(value1, value2, ...),

```sql
insert into actor
values(1,'PENELOPE','GUINESS','2006-02-15 12:34:33'),( 2,'NICK','WAHLBERG','2006-02-15 12:34:33');
```



方法二：利用 UNION SELECT 批量插入

```sql
insert into actor
select 1,'PENELOPE','GUINESS','2006-02-15 12:34:33'
union 
select 2,'NICK','WAHLBERG','2006-02-15 12:34:33';
```

### 36.**创建一个actor_name表**

对于如下表actor，其对应的数据为:

| actor_id | first_name | last_name | last_update         |
| :------- | :--------- | :-------- | :------------------ |
| 1        | PENELOPE   | GUINESS   | 2006-02-15 12:34:33 |
| 2        | NICK       | WAHLBERG  | 2006-02-15 12:34:33 |

请你创建一个actor_name表，并且将actor表中的所有first_name以及last_name导入该表. 

actor_name表结构如下：

| 列表       | 类型        | 是否为NULL | 含义 |
| :--------- | :---------- | :--------- | :--- |
| first_name | varchar(45) | not null   | 名字 |
| last_name  | varchar(45) | not null   | 姓氏 |

```sql
create table actor_name
select first_name ,last_name from actor;
```

### 37.**对first_name创建唯一索引uniq_idx_firstname**

针对如下表actor结构创建索引： 

(注:在 SQLite 中,除了重命名表和在已有的表中添加列,ALTER TABLE 命令不支持其他操作， 

mysql支持ALTER TABLE创建索引)

```sql
CREATE TABLE actor  (
   actor_id  smallint(5)  NOT NULL PRIMARY KEY,
   first_name  varchar(45) NOT NULL,
   last_name  varchar(45) NOT NULL,
   last_update  datetime NOT NULL);
```

对first_name创建唯一索引uniq_idx_firstname，对last_name创建普通索引idx_lastname 

```sql
create unique index uniq_idx_firstname on actor(first_name);
create index idx_lastname on actor(last_name);
```

### 38.**针对actor表创建视图actor_name_view**

针对actor表创建视图actor_name_view，只包含first_name以及last_name两列，并对这两列重新命名，first_name为first_name_v，last_name修改为last_name_v：

```sql
CREATE TABLE  actor  (
   actor_id  smallint(5)  NOT NULL PRIMARY KEY,
   first_name  varchar(45) NOT NULL,
   last_name  varchar(45) NOT NULL,
   last_update datetime NOT NULL);
```

```sql
create view actor_name_view as 
select 
first_name as first_name_v
,last_name as last_name_v
from actor;

create view actor_name_view (first_name_v,last_name_v) as 
select 
first_name ,last_name
from actor;
```

### 39.针对上面的salaries表emp_no字段创建索引idx_emp_no，查询emp_no为10005,

```sql
# 针对salaries表emp_no字段创建索引idx_emp_no，查询emp_no为10005, 使用强制索引。
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
create index idx_emp_no on salaries(emp_no);
```

```sqlite
select 
* from salaries
indexed by idx_emp_no
where emp_no='10005'
```

```mysql
select 
* from salaries
force index (idx_emp_no)
where emp_no='10005'
```

