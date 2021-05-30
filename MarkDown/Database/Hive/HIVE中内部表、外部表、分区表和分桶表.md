# HIVE中内部表、外部表、分区表和分桶表



> 目录
>  一、Hive建表语法
>  二、内部表外部表
>  三、分区表
>  四、分桶表

Hive在建表时可指定内部表、外部表、分区表和分桶表，所以在学习这四种表之前，先一起了解Hive建表语法

### 一、Hive建表语法

Hive建表方式共有三种：直接建表法、查询建表法、like建表法，下面讲解**`直接建表法`**

**’[]’ 表示可选，’|’ 表示二选一**



```csharp
创建表的语句：
Create [EXTERNAL] TABLE [IF NOT EXISTS] table_name 
//EXTERNAL 关键字可以让用户创建一个外部表，如果不指定关键字EXTERNAL则为内部表
//如：create table IF NOT EXISTS test_bucket
[(col_name data_type [COMMENT col_comment], ...)] 
//col_nam表字段，data_type字段类型，[COMMENT col_comment]字段注释 ， ...为其他字段
//如：(word string,num bigint )
[COMMENT table_comment] 
//表注释，COMMENT关键字，table_comment表解释具体内容
[PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] 
//PARTITIONED BY分区
[CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC],...)]INTO num_buckets BUCKETS]
//分桶，通过 clustered by(字段名) into bucket_num buckets 分桶，意思是根据字段名分成bucket_num个桶
//如：clustered by(id) into 4 buckets
[ROW FORMAT row_format] 
//加载数据文件到hive表时，数据文件字段于字段的分隔符
//如：row format delimited fields terminated by '\t'
[STORED AS file_format] 
[LOCATION hdfs_path]
//指定表存放的HDFS位置，如果不指定存放在默认路径，内部表默认路径/user/hive/warehouse
```

##### 建表语句解释：

- CREATE TABLE 创建一个指定名字的表。
- EXTERNAL 关键字可以让用户创建一个外部表，无EXTERNAL 关键字则为内部表。在删除表的时候，内部表的元数据和数据会被一起删除，而外部表只删除元数据，不删除数据。
- 有分区的表可以在创建的时候使用 PARTITIONED BY 语句。一个表可以拥有一个或者多个分区，每一个分区单独存在一个目录下。

##### 案例：



```sql
//01-创建orders_part外部表
create  EXTERNAL table orders_part(
order_id string comment '订单编号',
user_id string comment '客户号',
order_number string  comment '订单数量',
order_date string comment '下单时间'
)
comment '测试_创建orders_part表'
partitioned by(order_date string)
row format delimited fields terminated by ','

//02-创建分桶表
create table test_bucket (
id int comment 'ID', 
name string comment '名字'
)
comment '测试分桶'
clustered by(id) into 4 buckets
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;
```

### 二、内部表外部表

通过建表**`语句、location位置、删除表`**三方面来学习外部表，内部表

#### 1、建表语句

**1.1、内部表**
 平时创建的普通表为内部表



```sql
create table `test_internal` (
id string comment 'ID', 
name string comment '名字'
)
comment '测试内部表'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;
```

**1.2、外部表**
 带`external`关键字的为`外部表`



```sql
create external table `test_external` (
id string comment 'ID', 
name string comment '名字'
)
comment '测试外部表'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;
```

**仅从建表语句上看，内部表和外部表的区别为是否带有`external关键字`**

#### 2、location

> 网上很多博客写的`外部表`建表语句中都带有location关键字，我这里单独分开来写。[我参考的是一个博主的文章]

- 其实不管是内部表还是外部表**都**可以**加location关键字**指定hive表的存储路径，当然也可以不加，从这点看是**无区别**的。
- 如果不加location那么不管是内部表还是外部表**都**会在**默认**的hive配置的**hdfs路径**下下新建一个和表名相同的文件夹。内部表默认存放路径：/user/hive/warehouse
- 如果加了location指定另外的文件夹，那么在查询时，该文件夹下对应的数据会加载到hive表里。

**如果不理解上面文字描述可以看下面案例**

##### 案例：

##### 01、准备数据

**将数据上传到HDFS**



```sql
//data.txt 数据
001,张三
002,李四
003,王五
```



```kotlin
hadoop fs -mkdir -p /tmp/hive_test/internal_location //创建Hdfs目录
hadoop fs -mkdir -p /tmp/hive_test/external_location
hadoop fs -put data.txt /tmp/hive_test/internal_location  //上传数据到HDFS
hadoop fs -put data.txt /tmp/hive_test/external_location //上传数据到HDFS文件夹
```

![img](https:////upload-images.jianshu.io/upload_images/2693156-152a96c225a76de6.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

##### 02、建表

**内部表**



```go
create table IF NOT EXISTS test_internal_location (
id string comment 'ID', 
name string comment '名字'
)
comment '测试内部表location'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
location '/tmp/hive_test/internal_location';
```

**外部表**



```go
create external table   IF NOT EXISTS test_external_location (
id string comment 'ID', 
name string comment '名字'
)
comment '测试外部表location'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
location '/tmp/hive_test/external_location';
```

![img](https:////upload-images.jianshu.io/upload_images/2693156-09fa5b16efb51d70.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

说明：hdfs 文件夹及对应下的数据和建表语句没有先后顺序，建表在前和在后都可以把数据加载出来，如果先建表的话，对应的文件夹如果不存在，则会自动建立文件夹。

#### 3、删除表

内部表和外部表的区别主要体现在删除表，将上面建立的2个表都删掉。



```rust
drop table test_internal_location;
drop table test_external_location;
```

看一下对应的hdfs路径有啥变化



![img](https:////upload-images.jianshu.io/upload_images/2693156-cf3203c3c10e9ffe.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

发现**外部表**external_location**文件夹存在**，而**内部表**的**文件夹没了**，这也就是内部表和外部表的区别

**内部表删除表**时，对应的**hdfs的路径**下的**文件会删掉**；外部表删除表时，对应的HDFS的路径下的文件则不会删掉。

##### 4、内部表外部表—总结

(1) **建表：**带有external关键字为外部表，否则为内部表
 (2) **location：**内部表和外部表建表时都可以自己指定location
 (3) **删除表：**外部表不会删除对应的数据，只会删除元数据信息，内部表则会删除元数据信息和对应的数据。

[**参考文章：Hive内部表和外部表，看了这个博主写的两篇文章，写得真的很好很好。**](https://links.jianshu.com/go?to=https%3A%2F%2Fdongkelun.com%2F2018%2F12%2F03%2FhiveInternalAndExternalTables%2F)

### 三、分区表

分区表一般在数据量比较大，且有明确的分区字段时使用，这样用分区字段作为查询条件查询效率会比较高。Hive分区分为**静态分区**和**动态分区**。

##### 1、为什么出现分区表？

假设有海量的数据保存在hdfs的某一个hive表名对应的目录下，使用hive进行操作的时候，往往会搜索这个目录下的所有的文件，这时会非常耗时。`(hive表对应hdfs目录下的文件数据都会被加载到hive表中）`

如果我们知道这些**数据**的某些**特征**，然后使用Hive进行操作的时候，就可以在**where**子句种**对这些特征**进行**过滤**，那么对**数据的操作**就会在**符合条件**的**子目录**下进行，其他**不符合条件**的**目录下的内容**就不会被**读取**。

在数据量非常大的时候，这样节省大量的时间，这种把表种的数据分散到子目录下的方式就是分区表。



![img](https:////upload-images.jianshu.io/upload_images/2693156-12fa9fd232f0967a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)



下面我们还是通过实战来学习分区表

### 案例

##### 2、创建分区表

静态分区和动态分区的建表语句是一样的



```go
create table test_partition (
name string comment '名字',
age int comment '年龄'
)
comment '测试分区'
partitioned by (provice string comment '省',city string comment '城市')
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;
```

用partitioned by指定创建的分区，多个分区意味着多级目录。

创建之后hdfs上的目录结构并不会立即发生变化，因为此时表中还没有数据，往表中插入数据之后，会发现在表名对应的目录下，会多出两级目录。

#### 3、插入语句

> 静态分区和动态分区的插入数据的语法是不一样的，下面讲解**静态分区**。
>  想学习动态分区可看这篇文章[**hive使用动态分区插入数据详解**](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2FSunWuKong_Hadoop%2Farticle%2Fdetails%2F81334556%3Futm_medium%3Ddistribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control%26depth_1-utm_source%3Ddistribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control)

##### 3.1 静态分区

静态分区是在语句中指定分区字段为某个固定值

##### 3.1.1 静态分区加载数据-INSERT INTO语法



```csharp
insert into table test_partition partition(provice = "hebei",city = "baoding") values ('张三',20);
insert into table test_partition partition(provice = "hebei",city = "baoding") values ('张三',20);
insert into table test_partition partition(provice = "hebei",city = "baoding") values ('李四',20);

insert into table test_partition partition(provice = "hebei",city = "handan") values ('张三',20);
insert into table test_partition partition(provice = "hebei",city = "handan") values ('张三',20);
insert into table test_partition partition(provice = "hebei",city = "handan") values ('李四',20);
```

##### 3.1.2 静态分区加载数据-LOAD DATA方法

data4.txt



```undefined
张三,23
李四,23
王五,23
```

加载本地文件到hive表中



```csharp
load data local inpath '/home/study/data4.txt' into table test_partition partition (provice = "hebei",city = "handan");
load data local inpath '/home/study/data4.txt' into table test_partition partition (provice = "hebei",city = "handan");
load data local inpath '/home/study/data4.txt' into table test_partition partition (provice = "sichuan",city = "chengdu");
```

##### 3.1.3 查看数据

![img](https:////upload-images.jianshu.io/upload_images/2693156-ddc946b7038bd791.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

> **hive的仓库路径:**   /user/hive/warehouse
>  **数据库名称:**    myhive.db myhive
>  **表名:** test_partition
>  **分区字段名:**provice,city

#### 总结：

- (1) 分区表的意思，其实想明白了就很简单。就是在系统上建立文件夹，把**分类数据**放在**不同文件夹**下面，**加快查询**速度。
- (2) 关键点1：partitioned by (provice String,city string); 创建表格时，指明了这是一个分区表。将建立双层目录，第一次目录的名字和第二层目录名字规则
   **PARTITIONED BY**子句中**定义列**，是**表**中**正式的列**，成为分区列。但是**数据文件**中并**没有**这些值，仅**代表目录**。
- (3) 关键点2：partition (provice = "hebei",city = "handan"); 上传数据时，把数据分别上传到不同分区中。也就是分别放在不同的子目录下。

[**参考文章1:Hive分区表学习总结**](https://links.jianshu.com/go?to=https%3A%2F%2Fdongkelun.com%2F2018%2F12%2F02%2FhivePartitionTable%2F)
 [**参考文章2:hive使用动态分区插入数据详解**](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2FSunWuKong_Hadoop%2Farticle%2Fdetails%2F81334556%3Futm_medium%3Ddistribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control%26depth_1-utm_source%3Ddistribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control)
 [**参考文章3:HIVE-分区表详解以及实例**](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fkouryoushine%2Fp%2F7801924.html)

### 四、分桶表

其实分区和分桶这两个概念对于初学者来说是比较难理解的,要想更好的学习分桶表,看文章的同时还需要自己操作才行。下面我们通过实操来学习分桶表`[可能刚看着代码有点迷惑,不过做到后面就理解了]`

也可以像我这样学习:看文章+实操+写文章总结,很多知识点我按这样学习后基本上不会忘记。

#### 1、分桶简介？

分桶规则：桶是通过对指定列进行哈希计算来实现的，对分桶字段值进行哈希，哈希值除以桶的个数求余，余数决定了该条记录在哪个桶中，也就是余数相同的在一个桶中。
 优点：1、提高join查询效率 2、提高抽样效率

#### 2、分桶操作

#### 2.1、创建桶表：

通过 clustered by(字段名) into bucket_num buckets 分桶，意思是根据字段名分成bucket_num个桶



```csharp
create table test_bucket (
id int comment 'ID', 
name string comment '名字'
)
comment '测试分桶'
clustered by(id) into 4 buckets
-- 注意分桶字段只能是建表中已有的字段，
-- 而分区表的字段必须是建表中没有的字段
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;
```

#### 2.2、设置Hive的分桶开关

打开Hive客户端，并在会话终端开启Hive分桶的开关：`set hive.enforce.bucketing=true`;(此设置为临时设置，一旦退出会话终端，再打开就会恢复默认设置false)



```cpp
set hive.enforce.bucketing=true;  ///强制分桶
```

![img](https:////upload-images.jianshu.io/upload_images/2693156-c0faf144c384a46b.png?imageMogr2/auto-orient/strip|imageView2/2/w/862/format/webp)

此开关打开之后，会**自动**根据**bucket(桶)个数**自动**分配Reduce task**的**个数**，Reduce个数与bucket个数一致。(此外，Reduce的个数还可以通过mapred.reduce.tasks进行设置，但是这方法不推荐在Hive分桶中使用)

#### 2.2.3、load data方法插入数据到分桶表

buckt_data.txt



```undefined
1,name1
2,name2
3,name3
4,name4
5,name5
6,name6
7,name7
8,name8
9,name9
```

load data 插入数据到分桶表



```bash
load data local inpath '/home/study/buckt_data.txt' into table test_bucket ;
```



![img](https:////upload-images.jianshu.io/upload_images/2693156-e7e32394083b1c9c.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)


 我们看到虽然设置了强制分桶，但实际test_bucket 表下面只有一个test_bucket 一个文件。**直接load data不会**有**分桶**的效果，这样和不分桶一样，在HDFS上只有一个文件。需要借助中间表才能实现



#### 2.2.3、借用中间表insert into方法插入数据到分桶表

创建中间表



```go
create table test (
id int comment 'ID', 
name string comment '名字'
)
comment '测试分桶中间表'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;
```

先将数据load到中间表



```bash
load data local inpath '/home/study/buckt_data.txt' into table test;
```

通过下面insert into的语句，将中间表的数据插入到分桶表中，这样会产生四个文件。



```csharp
insert into test_bucket select * from test;
```

![img](https:////upload-images.jianshu.io/upload_images/2693156-222b621531ed6d2f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

借的别人的图片,不过这个博主写的文章真的好

从上面图片可以看见:
 (1) **桶**是以**文件**的形式存在的，而不是像**分区**那样以**文件夹**的形式存在。
 (2) 除以4**余数相同**的确实在一个文件里，也就是在一个桶中

### 总结：

(1) 其实桶的概念就是MapReduce的分区的概念，两者完全相同。物理上**每个桶**就是目录里的**一个文件**，一个**作业产生的桶**（输出文件）数量和**reduce任务个数**相同。
 (2) 桶是按照数据内容的某个值进行分桶，把一个大文件散列称为一个个小文件。
 (3) 这些小文件可以单独排序。如果另外一个表也按照同样的规则分成了一个个小文件。**两个表join**的时候，就不必要扫描整个表，只需要**匹配相同分桶**的数据即可。效率当然大大提升。
 同样，对**数据抽样**的时候，也不需要扫描整个文件。只需要对每个分区按照相同规则抽取一部分数据即可。

[**参考文章1:Hive分桶表学习总结**](https://www.jianshu.com/p/922e2e37ae22)
 [**参考文章2:HIVE-分桶表的详解和创建实例**](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fkouryoushine%2Fp%2F7809299.html)
 [**参考文章3:Hive分桶表的使用场景以及优缺点分析,想了解概念看该篇文章**](https://links.jianshu.com/go?to=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F93728864)

> 关于HIVE中内部表、外部表、分区表和分桶表就总结完了,后期还得继续努力学习呀.问题还是得一个一个的解决.加油啦~~~
>  做仰卧起坐去了,周末愉快啦.
>  这个冬天真冷



作者：夜希辰
链接：https://www.jianshu.com/p/e4e10b24aec7
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。