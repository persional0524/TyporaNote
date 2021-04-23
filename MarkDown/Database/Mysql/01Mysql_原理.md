# Mysql数据库篇



## 1、Mysql有哪几种数据存储引擎？有什么区别？

Mysql通过show ENGINES指令可以看到所有支持的数据库存储引擎，最为常用的就是MyISAM和InnoDB两种。

MyISAM和InnoDB区别：

1. 存储文件。MyISAM每个表有两个文件，MYD和MYISAM文件。MYD是数据文件，MYISAM是索引文件。而InnoDB每个表只有一个文件，idb。

## 2、Mysql的锁有哪些？什么是间隙锁？

