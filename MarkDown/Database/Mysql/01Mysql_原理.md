# Mysql数据库篇



## 1、Mysql有哪几种数据存储引擎？有什么区别？

Mysql通过show ENGINES指令可以看到所有支持的数据库存储引擎，最为常用的就是MyISAM和InnoDB两种。

MyISAM和InnoDB区别：

1. 存储文件。MyISAM每个表有两个文件，MYD和MYISAM文件。MYD是数据文件，MYISAM是索引文件。而InnoDB每个表只有一个文件，idb。

2. InnoDB支持事务，支持行级锁、支持外键。

3. InnoDB支持XA事务。

4. InnoDB支持savePoints。

   

## 2、Mysql的锁有哪些？什么是间隙锁？

从锁的粒度来区分：

1. 行锁：
   1. 共享锁：
   2. 排他锁：
   3. 自增锁：
2. 表锁：
3. 全局锁：

常见锁算法：

1. 记录锁：
2. 间隙锁：
3. Next-key：



