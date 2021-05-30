### 42.删除emp_no重复的记录，只保留最小的id对应的记录。

题目描述

删除emp_no重复的记录，只保留最小的id对应的记录。

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
        and a.id > b.id) t)；



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



### 45.将titles_test表名修改为titles_2017







