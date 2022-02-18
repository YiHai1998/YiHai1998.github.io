# Spark安装

官网：http://spark.apache.org/

下载：https://www.apache.org/dyn/closer.lua/spark/spark-2.4.7/spark-2.4.7-bin-hadoop2.7.tgz

解压安装

```
tar -zxvf spark-2.4.7-bin-hadoop2.7.tgz -C /opt/module/
mv spark-2.4.7-bin-hadoop2.7/ spark
```

修改配置`spark-defaults.conf`

```
mv spark-defaults.conf.template  spark-defaults.conf
vim spark-defaults.conf
```

添加如配置

```
spark.master                     yarn
spark.eventLog.enabled           true
spark.eventLog.dir               hdfs://tsingdata01:8020/spark_directory
spark.driver.memory              1g
spark.executor.memory            1g
```

hadoop集群上提前创建spark_directory日志路径

```
hadoop fs -mkdir /spark_directory
```

修改配置`spark-env.sh`

```
mv spark-env.sh.template spark-env.sh
vim spark-env.sh
```

文件末尾加入以下内容

```
SPARK_HISTORY_OPTS="-Dspark.history.fs.logDirectory=hdfs://tsingdata01:8020/spark_directory"

export JAVA_HOME=/opt/module/jdk1.8.0_212
export HADOOP_CONF_DIR=/opt/module/hadoop-3.1.3/etc/hadoop
export SPARK_MASTER_HOST=tsingdata01
export SPARK_MASTER_PORT=7077
export SPARK_WORKER_CORES=1
export SPARK_WORKER_MEMORY=1g
export SPARK_MASTER_WEBUI_PORT=8088
```

拷贝jar包，将Hive中/opt/module/hive/lib/datanucleus-*.jar包拷贝到Spark的/opt/module/spark/jars路径

```
cp /opt/module/hive/lib/datanucleus-*.jar /opt/module/spark/jars
```

拷贝配置文件，将Hive中/opt/module/hive/conf/hive-site.xml包拷贝到Spark的/opt/module/spark/conf路径

```
cp /opt/module/hive/conf/hive-site.xml  /opt/module/spark/conf/
```

修改`slaves`

```
mv slaves.template slaves
vim slaves
```

修改内容如下

```
tsingdata01
tsingdata02
tsingdata03
```

分发spark安装目录到其他节点

```
xsync /opt/module/spark
```

启动

```
sbin/start-all.sh
```

运行实例

```
bin/run-example SparkPi 2>&1 | grep "Pi is roughly"
```

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618882205160-57b5f3ce-90f4-4db0-b7eb-f00de72a9230.png)

`报错`：ERROR spark.SparkContext: Error initializing SparkContext

https://blog.csdn.net/weixin_34049948/article/details/93035958