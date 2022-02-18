# MapReduce概述

## 定义

MapReduce是一个==分布式==运算程序的编程==框架==，是用户开发“基于Hadoop的数据分析应用”的核心框架。

MapReduce核心功能是`将用户编写的业务逻辑代码和自带默认组件`整合成一个完整的分布式运算程序，并发运行在一个Hadoop集群上。

## 优缺点

优点

```
1、易于编程。 用户只关心，业务逻辑。实现框架的接口。
2、良好扩展性:可以动态增加服务器，解决计算资源不够问题。
3、高容错性。任何一台机器挂摅，可以将任务转移到其他节点。
4、适合海量数据计算(TB/PB)。几千台服务器共同计算。
```

缺点

```
1、不擅长实时计算。Mysql
2、不擅长流式计算。Sparkstreaming flink
3、不擅长DAG有向无环图计算。
```

## 核心思想





## 编程规范

==用户==编写的程序分成三个部分：Mapper、 Reducer 和 Driver。

1、 Mapper阶段

```
(1) 用户自定义的Mapper要继承自己的父类
(2) Mapper的输入数据是KV对的形式(KV的类型可自定义)
(3) Mapper中的业务逻辑写在map()方法中
(4) Mapper的输出数据是KV对的形式(KV 的类型可自定义)
(5) map(方法(MapTask进程) 对每一个<K,V> 调用一次
```

2、Reducer阶段

```
(1) 用户自定义的Reducer要继承自己的父类
(2) Reducer的输入数据类型对应Mapper的输出数据类型，也是KV
(3) Reducer的业务逻辑写在reduce()方法中
(4) ReduceTask进程对每一组相同k的<k,v>组调用一次reduce()方法
```



### WordCount案例

环境准备

```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>3.1.3</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
    </dependencies>
```

新建三个类 WordCountMapper WordCountReducer WordCountDriver

#### WordCountMapper

```java
/**
 * KEYIN, map阶段输入的key的类型:      LongWritable
 * VALUEIN, map阶段输入的value类型:   Text
 * KEYOUT, map阶段输出的Key类型:      Text
 * VALUEOUT, map阶段输入的value类型: IntWritable
 */

public class WordCountMapper extends Mapper<LongWritable, Text, Text, IntWritable> {
    private Text outK = new Text();
    private IntWritable outV = new IntWritable(1); // map阶段不需要聚合，所以填1
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        // 1 获取1行
        // glong glong
        String line = value.toString();

        // 2 切割
        // glong
        // glong
        String[] words = line.split(" ");

        // 3 循环写出
        for (String word : words) {
            // 封装
            outK.set(word);

            // context衔接了map和reduce
            // 写出
            context.write(outK, outV);
        }
    }
}
```

#### WordCountReducer

```java
/**
 * reduce的输入是map的输出
 * KEYIN, reduce阶段输入的key的类型:      Text
 * VALUEIN, reduce阶段输入的value类型:   IntWritable
 * KEYOUT, reduce阶段输出的Key类型:      Text
 * VALUEOUT, reduce阶段输入的value类型: IntWritable
 */

public class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
    private IntWritable outV = new IntWritable();
    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        int sum = 0;
        // glong, (1,1)
        // 累加
        for (IntWritable value : values) {
            sum += value.get();
        }
        outV.set(sum);
        // 写出
        context.write(key, outV);
    }
}
```

#### WordCountDriver	

```java
public class WordCountDriver {
    public static void main(String[] args) throws IOException, InterruptedException, ClassNotFoundException {
        // 1 获取job
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf);

        // 2 设置jar路径
        job.setJarByClass(WordCountDriver.class);

        // 3 关联mapper和reducer
        job.setMapperClass(WordCountMapper.class);
        job.setReducerClass(WordCountReducer.class);

        // 4 设置map输出的kv类型
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);

        // 5 设置最终输出的kv类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        // 6 设置输入路径、输出路径
        // 导入名字长的那个包
        FileInputFormat.setInputPaths(job, new Path("hadoop/src/main/resources/inputword"));
        FileOutputFormat.setOutputPath(job, new Path("hadoop/src/main/resources/output1")); // 不要创建

        // 7 提交job
        boolean result = job.waitForCompletion(true);
        System.exit(result ? 0 : 1);
    }
}
```



在inputword文件夹下面创建test.txt

```
glong glong
```

运行之后，查看output1文件夹下part-r-00000

```
glong	1
glong	1
```

