```java
package com.liu.wc;


import org.apache.flink.api.common.functions.FlatMapFunction;
import org.apache.flink.api.java.DataSet;
import org.apache.flink.api.java.ExecutionEnvironment;
import org.apache.flink.api.java.operators.DataSource;
import org.apache.flink.api.java.tuple.Tuple2;
import org.apache.flink.util.Collector;

/**
 * @author Liutao0705@live.com
 * @date 2021/5/18 10:44 下午
 */


// 批处理,DataSet-->离线数据api
public class WordCount {
    public static void main(String[] args) throws Exception {
        //创建执行环境
        ExecutionEnvironment env = ExecutionEnvironment.getExecutionEnvironment();

        //从文件中读取数据
        String inputPath = "/Users/liutao/IdeaProjects/FlinkTutorial/src/main/resources/hello";
        //DataSource -->Operator -->
        //DataSource<String> stringDataSource = env.readTextFile(inputPath); .val 快捷键
        DataSet<String> inputDataSet = env.readTextFile(inputPath);

        //对数据集进行处理，按空格分词展开，转换成（word，1）二元组进行统计
        //flatMap(FlatMapFunction<T, R> flatMapper)
        // <T, R> 范性
        // void flatMap(T var1, Collector<O> var2) throws Exception; Collector 收集器

        DataSet<Tuple2<String, Integer>> resultSet = inputDataSet.flatMap(new MyFlatMapper())
                //传位置
                .groupBy(0)//按照第一个位置的word分组
                .sum(1);//将第二个位置上的数据求和

        resultSet.print();


    }

    //自定义类，实现MyFlatMapper接口
    public static class MyFlatMapper implements FlatMapFunction<String, Tuple2<String, Integer>> {
        @Override
        public void flatMap(String value, Collector<Tuple2<String, Integer>> out) throws Exception {
            //按空格分词
            String[] words = value.split(" ");
            //遍历所有的word。包成二元组输出
            for (String word : words) {
                out.collect(new Tuple2<>(word, 1));
            }

        }
    }


}

```

