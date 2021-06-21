### 50.**将employees表中的所有员工的last_name和first_name通过引号连接起来。**

将employees表中的所有员工的last_name和first_name通过(')连接起来。(sqlite不支持concat，请用||实现，mysql支持concat)

```mysql
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

输出格式:

| name               |
| :----------------- |
| Facello'Georgi     |
| Simmel'Bezalel     |
| Bamford'Parto      |
| Koblick'Chirstian  |
| Maliniak'Kyoichi   |
| Preusig'Anneke     |
| Zielinski'Tzvetan  |
| Kalloufi'Saniya    |
| Peac'Sumant        |
| Piveteau'Duangkaew |
| Sluis'Mary         |

```mysql
select 
concat(last_name,'\'',first_name) as name 
from employees;
```

### 51.**查找字符串 10,A,B 中逗号,出现的次数cnt**

查找字符串'10,A,B' 中逗号','出现的次数cnt。

```mysql
select 2 as cnt ;


select (length("10,A,B") - length(replace("10,A,B",",",""))) as cnt;
```

属于技巧题，即**先用replace函数将原串中出现的子串用空串替换，再用原串长度减去替换后字符串的长度，最后除以子串的长度（本题中此步可省略，若子串长度大于1则不可省）**

```mysql
SELECT (length("10,A,B")-length(replace("10,A,B",",","")))/length(",") AS cnt;
```

### 52.**获取Employees中的first_name**

获取Employees中的first_name，查询按照first_name最后两个字母，按照升序进行排列

```sql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

输出格式：

| first_name |
| :--------: |
| Chirstian  |
|  Tzvetan   |
|  Bezalel   |
| Duangkaew  |
|   Georgi   |
|  Kyoichi   |
|   Anneke   |
|   Sumant   |
|    Mary    |
|   Parto    |
|   Saniya   |

```sql
select 
first_name
from employees
order by substring(first_name,-2,2);

select 
first_name
from employees
order by substr(first_name,-2,2);

select first_name from employees
order by right(first_name,2);
```

**三种方法都可以用**
LEFT(s,n)返回字符串 s 的前 n 个字符
RIGHT(s,n)返回字符串 s 的后 n 个字符

### 53.按照dept_no进行汇总，属于同一个部门的emp_no按照逗号进行连接，结果给出dept_no以及连接出的结果employees

按照dept_no进行汇总，属于同一个部门的emp_no按照逗号进行连接，结果给出dept_no以及连接出的结果employees

```sql
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));


INSERT INTO dept_emp VALUES(10001,'d001','1986-06-26','9999-01-01');
INSERT INTO dept_emp VALUES(10002,'d001','1996-08-03','9999-01-01');
INSERT INTO dept_emp VALUES(10003,'d004','1995-12-03','9999-01-01');
INSERT INTO dept_emp VALUES(10004,'d004','1986-12-01','9999-01-01');
INSERT INTO dept_emp VALUES(10005,'d003','1989-09-12','9999-01-01');
INSERT INTO dept_emp VALUES(10006,'d002','1990-08-05','9999-01-01');
INSERT INTO dept_emp VALUES(10007,'d005','1989-02-10','9999-01-01');
INSERT INTO dept_emp VALUES(10008,'d005','1998-03-11','2000-07-31');
INSERT INTO dept_emp VALUES(10009,'d006','1985-02-18','9999-01-01');
INSERT INTO dept_emp VALUES(10010,'d005','1996-11-24','2000-06-26');
INSERT INTO dept_emp VALUES(10010,'d006','2000-06-26','9999-01-01');

```

输出格式:

| dept_no | employees         |
| :------ | :---------------- |
| d001    | 10001,10002       |
| d002    | 10006             |
| d003    | 10005             |
| d004    | 10003,10004       |
| d005    | 10007,10008,10010 |
| d006    | 10009,10010       |

```mysql
#本题使用到的函数是group_concat()
#group_concat()函数介绍：
#语法: group_concat([distinct] 字段 [order by 排序字段 asc/desc]  separator  '分隔符')
#函数默认的分隔符是 ","，默认可以省略
#案例可以参考本题。



#当然，单纯以这道题而论，由于存在emp_no和dept_no的联合主键，那么不需要去考虑去重的问题，而同一dep_no的emp_no恰好也是顺序自增的(本题的测试数据如下所示),所以刚好避开了这两块，但就题目一般性而论，这是必须考虑的：

## 函数默认以','进行连接， 可简写为：
select 
a.dept_no
,group_concat(a.emp_no) as employees
from dept_emp a
group by a.dept_no;

select 
a.dept_no
,group_concat(distinct a.emp_no order by a.emp_no separator ',') as employees
from dept_emp a
group by a.dept_no;
```

### 54.**查找排除当前最大、最小salary之后的员工的平均工资avg_salary**

查找排除最大、最小salary之后的当前(to_date = '9999-01-01' )员工的平均工资avg_salary。

```sql
CREATE TABLE `salaries` ( `emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

如：

```mysql
INSERT INTO salaries VALUES(10001,85097,'2001-06-22','2002-06-22');
INSERT INTO salaries VALUES(10001,88958,'2002-06-22','9999-01-01');
INSERT INTO salaries VALUES(10002,72527,'2001-08-02','9999-01-01');
INSERT INTO salaries VALUES(10003,43699,'2000-12-01','2001-12-01');
INSERT INTO salaries VALUES(10003,43311,'2001-12-01','9999-01-01');
INSERT INTO salaries VALUES(10004,70698,'2000-11-27','2001-11-27');
INSERT INTO salaries VALUES(10004,74057,'2001-11-27','9999-01-01');
```

输出格式:

| avg_salary |
| :--------: |
|   73292    |



```sql
select avg(salary) as avg_salary
from salaries
where to_date = '9999-01-01'
and salary != (select min(salary) from salaries where to_date = '9999-01-01')
and salary != (select max(salary) from salaries where to_date = '9999-01-01');



select avg(salary) avg_salary
from salaries 
where to_date = '9999-01-01' 
and salary not in (select max(salary) from salaries where to_date = '9999-01-01')
and salary not in (select min(salary) from salaries where to_date = '9999-01-01');
```







### 55.**分页查询employees表，每5行一页，返回第2页的数据**

分页查询employees表，每5行一页，返回第2页的数据

```sql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

每页显示PageSize条记录

> > 第PageSize页:(PageNo-1)*PageSize,PageSize

**>如**：
每页显示3条记录（长度为3）
第1页：0，3 包含页：1，2，3 limit 0,3
第2页：3，3 包含页：4，5，6 limit 3,3
第3页：6，3 包含页：7，8，9 limit 6,3

**具体语句**：

```sql
SELECT * FROM TABLE_NAME LIMIT (PageNO-1)*PageSize,PageSize;
```

**本题解答：**

```sql
SELECT * FROM employees ORDER BY emp_no LIMIT  5,5;
```

### 	57.**使用含有关键字exists查找未分配具体部门的员工的所有信息。**

使用含有关键字exists查找未分配具体部门的员工的所有信息。

```sql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```

输出格式:

| emp_no | birth_date | first_name | last_name | gender | hire_date  |
| :----- | :--------- | :--------- | :-------- | :----- | :--------- |
| 10011  | 1953-11-07 | Mary       | Sluis     | F      | 1990-01-22 |



```mysql
select
a.emp_no
,a.birth_date
,a.first_name
,a.last_name
,a.gender
,a.hire_date
from employees a 
where not exists (select
                 b.emp_no
                 from dept_emp  b where 
                 a.emp_no = b.emp_no);
```



### 59.**获取有奖金的员工相关信息。**

获取有奖金的员工相关信息。

```sql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

create table emp_bonus(
emp_no int not null,
received datetime not null,
btype smallint not null);
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL, PRIMARY KEY (`emp_no`,`from_date`));
```

给出emp_no、first_name、last_name、奖金类型btype、对应的当前薪水情况salary以及奖金金额bonus。 bonus类型btype为1其奖金为薪水salary的10%，btype为2其奖金为薪水的20%，其他类型均为薪水的30%。 当前薪水表示to_date='9999-01-01'
输出格式:

| emp_no | first_name | last_name | btype | salary | bonus   |
| :----- | :--------- | :-------- | :---- | :----- | :------ |
| 10001  | Georgi     | Facello   | 1     | 88958  | 8895.8  |
| 10002  | Bezalel    | Simmel    | 2     | 72527  | 14505.4 |
| 10003  | Parto      | Bamford   | 3     | 43311  | 12993.3 |
| 10004  | Chirstian  | Koblick   | 1     | 74057  | 7405.7  |

