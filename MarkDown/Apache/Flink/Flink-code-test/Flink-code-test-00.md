```java
package com.liu.wc;

import org.apache.flink.api.java.DataSet;
import org.apache.flink.api.java.tuple.Tuple2;
import org.apache.flink.api.java.utils.ParameterTool;
import org.apache.flink.streaming.api.datastream.DataStream;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * @author Liutao0705@live.com
 * @date 2021/5/19 12:37 上午
 */
public class StreamingWordCount {

    public static void main(String[] args) throws Exception {
        //创建流处理执行环境
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        //并行度=8,=1 就是单线程，和批处理就很像
        env.setParallelism(1);

        //从文件中读取数据
//        String inputPath = "/Users/liutao/IdeaProjects/FlinkTutorial/src/main/resources/hello";
//        DataStream<String> inputDataStream = env.readTextFile(inputPath);
        
        //用 ParameterTool 工具从去程序启动参数中提取配置项
        ParameterTool parameterTool = ParameterTool.fromArgs(args);
        String host = parameterTool.get("host");
        int port = parameterTool.getInt("port");


        //从scoket 文本中读取数据
        //DataStream<String> inputDataStream = env.socketTextStream("localhost", 7777);
        DataStreamSource<String> inputDataStream = env.socketTextStream(host, port);

        //基于数据流进行转换计算
        DataStream<Tuple2<String, Integer>> resultStream = inputDataStream.flatMap(new WordCount.MyFlatMapper())
                .keyBy(0)
                .sum(1).setParallelism(2);


        resultStream.print().setParallelism(1);

        //执行任务
        env.execute();

        /*
         * 13> 当前，并行执行的线程的编号，对应的分区编号，默认并行度，当前笔记本的核心数量定。8-16
         * */
    }
}

```

