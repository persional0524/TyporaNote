### 40.在last_update后面新增加一列名字为create_date

存在actor表，包含如下列信息：

```mysql
CREATE TABLE  actor  (
   actor_id  smallint(5)  NOT NULL PRIMARY KEY,
   first_name  varchar(45) NOT NULL,
   last_name  varchar(45) NOT NULL,
   last_update  datetime NOT NULL);
```

现在在last_update后面新增加一列名字为create_date, 类型为datetime, NOT NULL，默认值为'2020-10-01 00:00:00’

```mysql
alter table actor add create_date datetime not null default '2020-10-01 00:00:0';

alter table actor add column create_date datetime NOT NULL default '2020-10-01 00:00:00';
```







### 41.**构造一个触发器audit_log**

构造一个触发器audit_log，在向employees_test表中插入一条数据的时候，触发插入相关的数据到audit中。

```sql
CREATE TABLE employees_test(
ID INT PRIMARY KEY NOT NULL,
NAME TEXT NOT NULL,
AGE INT NOT NULL,
ADDRESS CHAR(50),
SALARY REAL
);
CREATE TABLE audit(
EMP_no INT NOT NULL,
NAME TEXT NOT NULL
);
```



```mysql
create trigger audit_log after insert on employees_test
for each row
begin
    insert into audit values(new.id,new.name);
end;
```







### 42.删除emp_no重复的记录，只保留最小的id对应的记录。

题目描述

==删除emp_no重复的记录，只保留最小的id对应的记录。==

```sql
CREATE TABLE IF NOT EXISTS titles_test (
id int(11) not null primary key,
emp_no int(11) NOT NULL,
title varchar(50) NOT NULL,
from_date date NOT NULL,
to_date date DEFAULT NULL);

insert into titles_test values ('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01');
```

删除后titles_test表为

| id   | emp_no | title           | from_date  | to_date    |
| ---- | ------ | --------------- | ---------- | ---------- |
| 1    | 10001  | Senior Engineer | 1986-06-26 | 9999-01-01 |
| 2    | 10002  | Staff           | 1996-08-03 | 9999-01-01 |
| 3    | 10003  | Senior Engineer | 1995-12-03 | 9999-01-01 |
| 4    | 10004  | Senior Engineer | 1995-12-03 | 9999-01-01 |

```sql
delete from titles_test where id in 
(
	/*得到应该删除的id结果集*/
    select id from 
    titles_test tt , 
    (/*查询出emp_no 对应的多个id的中的最小的id*/
		select emp_no,min(id) as min_id from titles_test
		group by emp_no
		having count(id) > 1
	) aa 
	where tt.id <> aa.min_id/*id 不是最小的,就是要删除的*/ and  tt.emp_no=aa.emp_no
);

delete from titles_test where id in (
    select
    id from (
        select
        a.id
        from 
        titles_test a,
        (
            select
            b.emp_no
            ,min(b.id) as id
            from titles_test b
            group by b.emp_no
            having count(b.emp_no) >1
        ) b
        where a.emp_no = b.emp_no
        and a.id > b.id) t);

delete from titles_test where id in (
    select id from (
select 
b.id
from titles_test b,(
select
a.emp_no
,min(a.id) id
from titles_test a
group by a.emp_no
having count(a.emp_no) >1) a
where b.emp_no = a.emp_no
and b.id > a.id) t);

```

### 43.将所有to_date为9999-01-01的全部更新为NULL,且from_date更新为2001-01-01。

将所有to_date为9999-01-01的全部更新为NULL,且 from_date更新为2001-01-01。

```sql
CREATE TABLE IF NOT EXISTS titles_test (
id int(11) not null primary key,
emp_no int(11) NOT NULL,
title varchar(50) NOT NULL,
from_date date NOT NULL,
to_date date DEFAULT NULL);

insert into titles_test values ('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01');
```

更新后的值:

titles_test 表的值：

| id   | emp_no | title           | from_date  | to_date |
| ---- | ------ | --------------- | ---------- | ------- |
| 1    | 10001  | Senior Engineer | 2001-01-01 | NULL    |
| 2    | 10002  | Staff           | 2001-01-01 | NULL    |
| 3    | 10003  | Senior Engineer | 2001-01-01 | NULL    |
| 4    | 10004  | Senior Engineer | 2001-01-01 | NULL    |
| 5    | 10001  | Senior Engineer | 2001-01-01 | NULL    |
| 6    | 10002  | Staff           | 2001-01-01 | NULL    |
| 7    | 10003  | Senior Engineer | 2001-01-01 | NULL    |

```sql
update titles_test set to_date = null,from_date='2001-01-01'
where to_date='9999-01-01';
```



### 44.将id=5以及emp_no=10001的行数据替换成id=5以及emp_no=10005,其他数据保持不变，使用replace实现。

将id=5以及emp_no=10001的行数据替换成id=5以及emp_no=10005,其他数据保持不变，**使用replace实现，直接使用update会报错**。

```sql
CREATE TABLE titles_test (
   id int(11) not null primary key,
   emp_no  int(11) NOT NULL,
   title  varchar(50) NOT NULL,
   from_date  date NOT NULL,
   to_date  date DEFAULT NULL);

insert into titles_test values
('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01');
```

已知id为5的该条数据为：

```sql
(``'5'``, ``'10001'``, ``'Senior Engineer'``, ``'1986-06-26'``, ``'9999-01-01'``),
```

所以，可以有以下几种方式：

##1、使用replace

```sql
UPDATE titles_test``SET emp_no = REPLACE(emp_no, ``10001``, ``10005``)``WHERE id = ``5``;
```

##2、使用insert

有遇到重复主键了就进行更新emp_no的值

```sql
INSERT INTO titles_test``  ``VALUES(``5``, ``10001` `,``'Senior Engineer'``, ``'1986-06-26'``, ``'9999-01-01'``)``  ``ON DUPLICATE KEY UPDATE emp_no = ``10005``;
```

##3、使用replace into

REPLACE INTO当遇到primary 或者 unique key 的时候，会首先进行update

```sql
REPLACE INTO titles_test``  ``VALUES(``5``, ``10005` `,``'Senior Engineer'``, ``'1986-06-26'``, ``'9999-01-01'``) ;
```

关于插入insert 、replace 的一些用法，可以见我的博客：





```mysql
REPLACE INTO titles_test
SELECT 5,10005,title,from_date,to_date
FROM titles_test
WHERE id=5 AND emp_no=10001;
```



### 45.将titles_test表名修改为titles_2017

将titles_test表名修改为titles_2017。

```sql
CREATE TABLE IF NOT EXISTS titles_test (
id int(11) not null primary key,
emp_no int(11) NOT NULL,
title varchar(50) NOT NULL,
from_date date NOT NULL,
to_date date DEFAULT NULL);

insert into titles_test values ('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01');
```



```mysql
alter table titles_test rename to titles_2017;

ALTER TABLE titles_test RENAME AS titles_2017;
```

### 46.在audit表上创建外键约束，其emp_no对应employees_test表的主键id

在audit表上创建外键约束，其emp_no对应employees_test表的主键id。 

(以下2个表已经创建了)

```sql
CREATE TABLE employees_test(
ID INT PRIMARY KEY NOT NULL,
NAME TEXT NOT NULL,
AGE INT NOT NULL,
ADDRESS CHAR(50),
SALARY REAL
);

CREATE TABLE audit(
EMP_no INT NOT NULL,
create_date datetime NOT NULL
);
```





```sql
drop table audit;
CREATE TABLE audit(
EMP_no INT NOT NULL,
create_date datetime NOT NULL,
foreign key(EMP_no) references employees_test(ID)
);
```



### 48.将所有获取奖金的员工当前的薪水增加10%

请你写出更新语句，将所有获取奖金的员工当前的(salaries.to_date='9999-01-01')薪水增加10%。(emp_bonus里面的emp_no都是当前获奖的所有员工)

```sql
create table emp_bonus(
emp_no int not null,
btype smallint not null);
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL, PRIMARY KEY (`emp_no`,`from_date`));
```

如：

```sql
INSERT INTO emp_bonus VALUES (10001,1);
INSERT INTO salaries VALUES(10001,85097,'2001-06-22','2002-06-22');
INSERT INTO salaries VALUES(10001,88958,'2002-06-22','9999-01-01');
```

更新后的结果: 

salaries:

| emp_no | salary  | from_date  | to_date    |
| ------ | ------- | ---------- | ---------- |
| 10001  | 85097   | 2001-06-22 | 2002-06-22 |
| 10001  | 97853.8 | 2002-06-22 | 9999-01-01 |

```mysql
update salaries set salary = salary * 1.1
where emp_no in (
    select emp_no from( 
select 
    a.emp_no
    from salaries a
    inner join emp_bonus b
    on a.emp_no = b.emp_no
)a where to_date = '9999-01-01');


update salaries set salary = salary * 1.1
where emp_no in 
(
    select emp_no from emp_bonus 
) and to_date = '9999-01-01';
```

