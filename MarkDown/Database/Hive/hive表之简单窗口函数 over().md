# hive表之简单窗口函数 over()

- 窗口函数通常是分析人员使用 hive ql 进行一些复杂逻辑计算时使用的特殊函数，其中 over() 通常与聚合函数共同使用，比如 count()、sum()、min()、max()、avg() 等。
- over() 具有一定的窗口语义，如：OVER(ROWS ((CURRENT ROW) | (UNBOUNDED) PRECEDING) AND (UNBOUNDED |(CURRENT ROW) ) FOLLOWING )
- over() 直接使用时，通常是指定全量数据，当我们想要按某列的不同值进行窗口划分时，可以在 over() 中加入 partition by 语句。
- 常用 over() 内部参数：



```ruby
1. PARTITION BY
2. ORDER BY
3. ROWS ((CURRENT ROW) | (UNBOUNDED) PRECEDING) AND (UNBOUNDED |(CURRENT ROW) ) FOLLOWING 
```

#### 示例

> 创建测试表并加载简单的示例数据进行演示：



```csharp
hive> create table recommend.test_window(logday string, userid string, score int)
    > row format delimited
    > Fields terminated by ',';
```



```undefined
logday      userid  score
20191020    11111   85
20191020    22222   83
20191020    33333   86
20191021    11111   87
20191021    22222   65
20191021    33333   98
20191022    11111   67
20191022    22222   34
20191022    33333   88
20191023    11111   99
20191023    22222   33
```

> 示例 1: 简单使用 over() 函数进行数据统计, 统计每个用户及表中数据的总数。



```csharp
hive> select logday, userid, score, count(*) over()  as total
    > from recommend.test_window;

OK
logday      userid  score total
20191023    22222   33  11
20191023    11111   99  11
20191022    33333   88  11
20191022    22222   34  11
20191022    11111   67  11
20191021    33333   98  11
20191021    22222   65  11
20191021    11111   87  11
20191020    33333   86  11
20191020    22222   83  11
20191020    11111   85  11
```

- 这里使用 over() 与 select count(*) 有相同的作用，好处就是，在需要列出所有列值时不用再进行一次关联。

> 示例 2: 当想要对每天的数据进行统计时，可以使用 partition by 按日期列对数据进行分区处理，如：over(partition by logday)



```csharp
hive> select logday, userid, count(userid) over(partition by logday) as total
    > from recommend.test_window;

OK
logday      userid  total
20191020    33333   3
20191020    22222   3
20191020    11111   3
20191021    33333   3
20191021    22222   3
20191021    11111   3
20191022    33333   3
20191022    22222   3
20191022    11111   3
20191023    22222   2
20191023    11111   2
```

- 这种用法与 select logday, count(userid) from recommend.test_window group by logday 具有相同的效果，但是当想要得到 userid 信息时，这种用法的优势就很明显。

> 示例 3: 下面我们想要得到从第一天到现在的所有 score 大于80分的用户总数，此时简单的分区不能满足需求，需要将 order by 和 窗口定义结合使用。



```csharp
hive>
    > select logday,
    >         count(userid) over(order by logday rows between unbounded preceding and current row)
    > from recommend.test_window
    > where score > 80;
    
OK
20191020    1
20191020    2
20191020    3
20191021    4
20191021    5
20191022    6
20191023    7
```

- 通过 over() 计算出按日期的累加值后，然后去每天的最大值就是需要的总的累加值。

> 示例 4：计算每个用户到当前日期分数大于80的天数。



```csharp
hive> select userid, logday, score, count(case when score>=80 then userid else null end) over(partition by userid                 order by logday rows between unbounded preceding and current row) as total
    > from recommend.test_window
    > order by logday, userid;
    
useri   logday      score   total
11111   20191020    85  1
22222   20191020    83  1
33333   20191020    86  1
11111   20191021    87  2
22222   20191021    65  1
33333   20191021    98  2
11111   20191022    67  2
22222   20191022    34  1
33333   20191022    88  3
11111   20191023    99  3
22222   20191023    33  1
```

#### 扩展

- 分区语句 partition by 除了这里使用的单列分区外，还可以使用多列分区。事实上，这里的分区跟创建 hive 分区表有异曲同工之妙。
- 窗口的划分除了上面使用的 rows between unbounded preceding and current row 之外，还有其他的使用场景，如：



```undefined
ROWS BETWEEN CURRENT ROW AND n FOLLOWING：从当前行到随后的n行  
ROWS BETWEEN n PRECEDING AND CURRENT ROW：从前n行到当前行  
ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING: 从当前行到结尾行 
```

#### 小结

- 窗口函数通常是在复杂的分析场景中使用的，熟练掌握其用法，可以大大提升分析人员的工作效率。