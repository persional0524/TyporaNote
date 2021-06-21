

# Hive lag()与lead() 函数

lag与lead函数是跟偏移量相关的两个分析函数，通过这两个函数可以在一次查询中取出同一字段的前N行的数据(lag)和后N行的数据(lead)作为独立的列,从而更方便地进行进行数据过滤。这种操作可以代替表的自联接，并且LAG和LEAD有更高的效率。

over()表示 lag()与lead()操作的数据都在over()的范围内，他里面可以使用partition by 语句（用于分组） order by 语句（用于排序）。partition by a order by b表示以a字段进行分组，再 以b字段进行排序，对数据进行查询。

例如：lead(field, num, defaultvalue) field需要查找的字段，num往后查找的num行的数据，defaultvalue没有符合条件的默认值。
　　
如：1、获取当前记录的id，以及下一条记录的id

```sql
select t.id id ,
       lead(t.id, 1, null) over (order by t.id)  next_record_id, t.cphm
from tb_test t       
  order by t.id asc;
```

如：2、获取当前记录的id，以及上一条记录的id

```sql
select t.id id ,
       lag(t.id, 1, null) over (order by t.id)  next_record_id, t.cphm
from tb_test t       
  order by t.id asc;
```

如：3、获取号牌号码相同的，当前记录的id与，下一条记录的id（使用partition by）

```sql
select t.id id, 
       lead(t.id, 1, null) over(partition by cphm order by t.id) next_same_cphm_id, t.cphm
from tb_test t
     order by t.id asc  ;
```

如：4、查询 cphm的总数，当create_date与下一条记录的create_date时间间隔不超过10分钟则忽略

```sql
select cphm, count(1) total from
(
select t.id, 
  t.create_date t1,
  lead(t.create_date,1, null) over( partition by  cphm order by create_date asc ) t2,  
  ( lead(t.create_date,1, null) over(  partition by  cphm order by create_date asc )  - t.create_date ) * 86400 as itvtime,
  t.cphm
from tb_test t 
  order by t.cphm, t.create_date asc
) tt
where tt.itvtime >= 600 or  tt.itvtime  is null
group by tt.cphm;

```

下面还有很多用法，就不一一列举了，简单介绍一下，和上面用法类似：

```sql
   count() over(partition by ... order by ...)：求分组后的总数。
　　max() over(partition by ... order by ...)：求分组后的最大值。
　　min() over(partition by ... order by ...)：求分组后的最小值。
　　avg() over(partition by ... order by ...)：求分组后的平均值。
　　lag() over(partition by ... order by ...)：取出前n行数据。　　

　　lead() over(partition by ... order by ...)：取出后n行数据。

　　ratio_to_report() over(partition by ... order by ...)：Ratio_to_report() 括号中就是分子，over() 括号中就是分母。

　　percent_rank() over(partition by ... order by ...)：

```

**over partition by与group by的区别：**

- group by是对检索结果的保留行进行==单纯分组==，一般和聚合函数一起使用例如max、min、sum、avg、count等一块用。
- partition by虽然也具有分组功能，但同时也具有其他的高级功能。