## 部署开始



本项目的griffin版本选择：0.6.0

griffin最新版本：0.7.0



下载griffin-0.6.0.tar.gz

```
wget https://github.com/apache/griffin/archive/griffin-0.6.0.tar.gz
```



目录分析，griffin目录下包括：

```
1. griffin-doc	负责存放Griffin的文档
2. measure		负责与spark交互，执行统计任务
3. service		使用spring boot作为服务实现
4. ui			负责给ui模块提供交互所需的restful api，保存统计任务，展示统计结果。
```



![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618882094790-d61ade9d-358e-4df0-9d1b-a90846706416.png)

源码导入构建完毕后，需要修改配置文件。

创建`mysql`数据库`quartz`

```
create database quartz;
```

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618882143935-ef45f23a-0aca-441c-b1de-dccc549998f6.png)

导入`service`模块下`src\main\resources\Init_quartz_mysql_innodb.sql`文件

## 安装配置

### griffin之外的集群配置

#### 设置环境变量

以下环境变量的设置是最终版本。

```
vim /etc/profile
```

```
#hadoop
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export PATH=$PATH:$HADOOP_HOME/bin
#hadoop配置文件目录
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop

#java
export JAVA_HOME=/usr/local/java
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JRE_HOME=$JAVA_HOME/jre

#hive
export HIVE_HOME=/opt/module/hive
export PATH=$PATH:$HIVE_HOME/bin

#livy命令目录
export LIVY_HOME=/opt/module/livy/bin

#spark目录
export SPARK_HOME=/opt/module/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

#scala
export SCALA_HOME=/usr/local/scala-2.12.4
export PATH=$PATH:$SCALA_HOME/bin
```

```
source /etc/profile
```

#### hadoop

略

#### scala

安装教程：https://blog.csdn.net/u012957549/article/details/79115439

#### spark

略

#### mysql

略

#### hive

略

#### livy

官网：http://livy.incubator.apache.org/

下载

解压

```
unzip apache-livy-0.7.1-incubating-bin.zip -C /opt/module/
```

重命名为livy

```
cd /opt/module/livy/conf

mv livy-client.conf.template livy-client.conf
mv livy-env.sh.template livy-env.sh
mv spark-blacklist.conf.template spark-blacklist.conf
mv log4j.properties.template log4j.properties
mv livy.conf.template livy.conf
```



```
vim livy-env.sh

# 添加的内容如下
export JAVA_HOME=/opt/module/jdk1.8.0_212
export SPARK_HOME=/opt/module/spark
export SPARK_CONF_DIR=$SPARK_HOME/conf
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
```



```
vim livy.conf

livy.server.host = tsingdata01
livy.server.port = 8998
livy.spark.master = yarn
livy.repl.enableHiveContext = true
livy.spark.deploy-mode = client
```



启动livy：

```
bin/livy-server start
```

登录：http://192.168.28.116:8998/



#### elasticSearch

安装教程：https://zhuanlan.zhihu.com/p/34894617

```
groupadd esgroup
useradd esuser -g esgroup
chown -R esuser:esgroup elasticsearch-5.6.3 

su -esuser（不能使用su esuser命令，否则刚才配置的java环境变量在esuser用户下不会生效）
cd elasticsearch-5.6.3
./bin/elasticsearch（可以在后面加 -d，即./bin/elasticsearch -d，可以后台运行）
```



测试 `Elasticsearch` 是否启动成功

```
curl localhost:9200
```

在ES里创建griffin索引，要在esuser下面敲命令行才行：

```
curl -XPUT http://localhost:9200/griffin -d '
{
    "aliases": {},
    "mappings": {
        "accuracy": {
            "properties": {
                "name": {
                    "fields": {
                        "keyword": {
                            "ignore_above": 256,
                            "type": "keyword"
                        }
                    },
                    "type": "text"
                },
                "tmst": {
                    "type": "date"
                }
            }
        }
    },
    "settings": {
        "index": {
            "number_of_replicas": "2",
            "number_of_shards": "5"
        }
    }
}
'
```

查询索引

```
curl -XGET http://localhost:9200/griffin?include_type_name=true
```

删除

```
curl -XDELETE http://localhost:9200/griffin
```

### 配置service模块

1、`src\main\resources\application.properties`

```properties
spring.application.name=griffin_service
#默认端口是8080，因为spark的端口也是8080，所以会有冲突！改成8085
server.port=8085
spring.datasource.url=jdbc:mysql://192.168.28.116:3306/quartz?useSSL=false&autoReconnect=true&failOverReadOnly=false
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.generate-ddl=true
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.jpa.show-sql=true
# Hive metastore
# 这里配置的值为`hive-site.xml`中的 hive.metastore.uris 配置项的值
hive.metastore.uris=thrift://tsingdata01:9083
hive.metastore.dbname=hive
hive.hmshandler.retry.attempts=15
hive.hmshandler.retry.interval=2000ms
#Hive jdbc
hive.jdbc.className=org.apache.hive.jdbc.HiveDriver
hive.jdbc.url=jdbc:hive2://localhost:10000/
hive.need.kerberos=false
hive.keytab.user=xxx@xx.com
hive.keytab.path=/path/to/keytab/file
# Hive cache time
cache.evict.hive.fixedRate.in.milliseconds=900000
# Kafka schema registry
kafka.schema.registry.url=http://localhost:8081
# Update job instance state at regular intervals
jobInstance.fixedDelay.in.milliseconds=60000
# Expired time of job instance which is 7 days that is 604800000 milliseconds.Time unit only supports milliseconds
jobInstance.expired.milliseconds=604800000
# schedule predicate job every 5 minutes and repeat 12 times at most
#interval time unit s:second m:minute h:hour d:day,only support these four units
predicate.job.interval=5m
predicate.job.repeat.count=12
# external properties directory location
external.config.location=
# external BATCH or STREAMING env
external.env.location=
# login strategy ("default" or "ldap")
login.strategy=default
# ldap
ldap.url=ldap://hostname:port
ldap.email=@example.com
ldap.searchBase=DC=org,DC=example
ldap.searchPattern=(sAMAccountName={0})

# hdfs default name【跟你hadoop中core-site.xml中保持一致】
#修改成namenode的地址
fs.defaultFS=hdfs://tsingdata01:8020
# elasticsearch配置
elasticsearch.host=tsingdata01
elasticsearch.port=9200
elasticsearch.scheme=http
# elasticsearch.user = user
# elasticsearch.password = password
# livy配置
livy.uri=http://tsingdata01:8998/batches
livy.need.queue=false
livy.task.max.concurrent.count=20
livy.task.submit.interval.second=3
livy.task.appId.retry.count=3
livy.need.kerberos=false
livy.server.auth.kerberos.principal=livy/kerberos.principal
livy.server.auth.kerberos.keytab=/path/to/livy/keytab/file
# yarn url配置
yarn.uri=http://192.168.28.117:8088
# griffin event listener
internal.event.listeners=GriffinJobEventHook

logging.file=logs/griffin-service.log
```

2、`src\main\resources\quartz.properties`

```
#使用的mysql
org.quartz.jobStore.driverDelegateClass=org.quartz.impl.jdbcjobstore.StdJDBCDelegate
```

3、`src\main\resources\sparkProperties.json`，这里就在找传到hdfs对应的包

```json
{
  "file": "hdfs://tsingdata01:8020/griffin/griffin-measure.jar",
  "className": "org.apache.griffin.measure.Application",
  "queue": "default",
  "numExecutors": 2,
  "executorCores": 1,
  "driverMemory": "1g",
  "executorMemory": "1g",
  "conf": {
    "spark.yarn.dist.files": "hdfs://tsingdata01:8020/home/spark_conf/hive-site.xml"
  },
  "files": [
  ]
}
```



4、`src\main\resources\env\env_batch.json`

```json
{
  "spark": {
    "log.level": "WARN"
  },
  "sinks": [
    {
      "name": "console",
      "type": "CONSOLE",
      "config": {
        "max.log.lines": 10
      }
    },
    {
      "name": "hdfs",
      "type": "HDFS",
      "config": {
        "path": "hdfs://tsingdata01:8020/griffin/persist",
        "max.persist.lines": 10000,
        "max.lines.per.file": 10000
      }
    },
    {
      "name": "elasticsearch",
      "type": "ELASTICSEARCH",
      "config": {
        "method": "post",
        "api": "http://tsingdata01:9200/griffin/accuracy",
        "connection.timeout": "1m",
        "retry": 10
      }
    }
  ],
  "griffin.checkpoint": []
}
```



5、`src\main\resources\env\env_streaming.json`

```json
{
  "spark": {
    "log.level": "WARN",
    "checkpoint.dir": "hdfs://tsingdata01:8020/griffin/checkpoint/${JOB_NAME}",
    "init.clear": true,
    "batch.interval": "1m",
    "process.interval": "5m",
    "config": {
      "spark.default.parallelism": 4,
      "spark.task.maxFailures": 5,
      "spark.streaming.kafkaMaxRatePerPartition": 1000,
      "spark.streaming.concurrentJobs": 4,
      "spark.yarn.maxAppAttempts": 5,
      "spark.yarn.am.attemptFailuresValidityInterval": "1h",
      "spark.yarn.max.executor.failures": 120,
      "spark.yarn.executor.failuresValidityInterval": "1h",
      "spark.hadoop.fs.hdfs.impl.disable.cache": true
    }
  },
  "sinks": [
    {
      "type": "CONSOLE",
      "config": {
        "max.log.lines": 100
      }
    },
    {
      "type": "HDFS",
      "config": {
        "path": "hdfs://tsingdata01:8020/griffin/persist",
        "max.persist.lines": 10000,
        "max.lines.per.file": 10000
      }
    },
    {
      "type": "ELASTICSEARCH",
      "config": {
        "method": "post",
        "api": "http://tsingdata01:9200/griffin/accuracy"
      }
    }
  ],
  "griffin.checkpoint": [
    {
      "type": "zk",
      "config": {
        "hosts": "node01:2181,node02:2181,node03:2181,node04:2181,",
        "namespace": "griffin/infocache",
        "lock.path": "lock",
        "mode": "persist",
        "init.clear": true,
        "close.clear": false
      }
    }
  ]
}
```



6、修改service模块的pom文件

```xml
#默认使用的是postgresql，这里切换到MySQL
 <!--   <dependency>
                    <groupId>org.postgresql</groupId>
                    <artifactId>postgresql</artifactId>
                    <version>${postgresql.version}</version>
                </dependency>-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.java.version}</version>
        </dependency>

#取消已经注释的mysql，profile
#<!--if you need mysql, please uncomment mysql-connector-java -->
<profiles>
        <!--if you need mysql, please uncomment mysql-connector-java -->
        <profile>
            <id>mysql</id>
            <activation>
                <property>
                    <name>mysql</name>
                </property>
            </activation>
        </profile>
        <profile>
            <id>dev</id>
            <activation>
                <property>
                    <name>dev</name>
                </property>
            </activation>
        </profile>
        <profile>
            <id>postgresql</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <property>
                    <name>prod</name>
                </property>
            </activation>
        </profile>
    </profiles>
    
#默认spring-boot-maven-plugins打包时是build-info修改为repackage，并配置mainClass
<plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${spring-boot-maven-plugin.version}</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <mainClass>org.apache.griffin.core.GriffinWebApplication</mainClass>
                    <executable>false</executable>
                </configuration>
            </plugin>
```

最终如下





### 配置measure模块

1、`src\main\resources\env-batch.json`

```json
{
  "spark": {
    "log.level": "WARN",
    "config": {
      "spark.master": "local[*]"
    }
  },
  "sinks": [
    {
      "name": "console",
      "type": "CONSOLE",
      "config": {
        "max.log.lines": 10
      }
    },
    {
      "name": "hdfs",
      "type": "HDFS",
      "config": {
        "path": "hdfs://tsingdata01:8020/griffin/batch/persist",
        "max.persist.lines": 10000,
        "max.lines.per.file": 10000
      }
    },
    {
      "name": "elasticsearch",
      "type": "ELASTICSEARCH",
      "config": {
        "method": "post",
        "api": "http://tsingdata01:9200/griffin/accuracy",
        "connection.timeout": "1m",
        "retry": 10
      }
    }
  ],
  "griffin.checkpoint": []
}
```



2、`src\main\resources\env-streaming.json`

```json
{
  "spark": {
    "log.level": "WARN",
    "checkpoint.dir": "hdfs://tsingdata01:8020/test/griffin/cp",
    "batch.interval": "2s",
    "process.interval": "10s",
    "init.clear": true,
    "config": {
      "spark.master": "local[*]",
      "spark.task.maxFailures": 5,
      "spark.streaming.kafkaMaxRatePerPartition": 1000,
      "spark.streaming.concurrentJobs": 4,
      "spark.yarn.maxAppAttempts": 5,
      "spark.yarn.am.attemptFailuresValidityInterval": "1h",
      "spark.yarn.max.executor.failures": 120,
      "spark.yarn.executor.failuresValidityInterval": "1h",
      "spark.hadoop.fs.hdfs.impl.disable.cache": true
    }
  },
  "sinks": [
    {
      "name": "consoleSink",
      "type": "CONSOLE",
      "config": {
        "max.log.lines": 100
      }
    },
    {
      "name": "hdfsSink",
      "type": "HDFS",
      "config": {
        "path": "hdfs://tsingdata01:8020/griffin/streaming/persist",
        "max.persist.lines": 10000,
        "max.lines.per.file": 10000
      }
    },
    {
      "name": "elasticSink",
      "type": "ELASTICSEARCH",
      "config": {
        "method": "post",
        "api": "http://tsingdata01:9200/griffin/accuracy"
      }
    }
  ],
  "griffin.checkpoint": [
    {
      "type": "zk",
      "config": {
        "hosts": "localhost:2181",
        "namespace": "griffin/infocache",
        "lock.path": "lock",
        "mode": "persist",
        "init.clear": true,
        "close.clear": false
      }
    }
  ]
}
```



3、`src\main\resources\config-streaming.json`

```json
{
  "name": "prof_streaming",
  "process.type": "streaming",
  "data.sources": [
    {
      "name": "source",
      "baseline": true,
      "connector": {
        "type": "kafka",
        "version": "0.8",
        "dataframe.name": "kafka",
        "config": {
          "kafka.config": {
            "bootstrap.servers": "tsingdata01:9092",
            "group.id": "group1",
            "auto.offset.reset": "smallest",
            "auto.commit.enable": "false"
          },
          "topics": "sss",
          "key.type": "java.lang.String",
          "value.type": "java.lang.String"
        },
        "pre.proc": [
          {
            "dsl.type": "df-ops",
            "in.dataframe.name": "kafka",
            "out.dataframe.name": "out1",
            "rule": "from_json"
          },
          {
            "dsl.type": "spark-sql",
            "in.dataframe.name": "out1",
            "out.datafrmae.name": "out3",
            "rule": "select name, age from out1"
          }
        ]
      },
      "checkpoint": {
        "file.path": "hdfs://tsingdata01:8020/griffin/streaming/dump/source",
        "info.path": "source",
        "ready.time.interval": "10s",
        "ready.time.delay": "0",
        "time.range": [
          "0",
          "0"
        ]
      }
    }
  ],
  "evaluate.rule": {
    "rules": [
      {
        "dsl.type": "griffin-dsl",
        "dq.type": "profiling",
        "out.dataframe.name": "prof",
        "rule": "select count(name) as `cnt`, max(age) as `max`, min(age) as `min` from source",
        "out": [
          {
            "type": "metric",
            "name": "prof"
          }
        ]
      },
      {
        "dsl.type": "griffin-dsl",
        "dq.type": "profiling",
        "out.dataframe.name": "grp",
        "rule": "select name, count(*) as `cnt` from source group by name",
        "out": [
          {
            "type": "metric",
            "name": "name_group",
            "flatten": "array"
          }
        ]
      }
    ]
  },
  "sinks": [
    "elasticSink"
  ]
}
```



### 编译以上两个模块

```markdown
1. 在idea中打开对应的terminal，进入到service目录
mvn -Dmaven.test.skip=true clean package
2. 进入到对应的measure目录
mvn -Dmaven.test.skip=true clean package

cd /opt/module
3. 将measure的jar包复制到对应的节点，并改名
mv measure-0.6.0.jar griffin-measure.jar
4. 上传到hdfs对应的目录，这样做的目的主要是因为spark在yarn集群上执行任务时，需要到HDFS的/griffin目录下加载griffin-measure.jar，避免发生类org.apache.griffin.measure.Application找不到的错误。
hdfs dfs -put griffin-measure.jar /griffin/

5. 将service的jar包上传到对应的Linux节点上，后台启动，启动之前，保证hadoop、hive、es、livy启动了
nohup java -jar service-0.6.0.jar>service.out 2>&1 &

6. 查看是否成功
tail -f service.out
```

> `注`：新版可以不使用如上的java -jar方式启动，这个是官网老的版本启动方式，新版直接执行脚本start.sh即可，详情见下



### 上传service-0.6.0.tar.gz

似乎没用

```
tar -zxvf service-0.6.0.tar.gz

# 下面的sh脚本启动后台，替换上面的java -jar
/opt/module/service-0.6.0/bin/start.sh
```



### 创建hdfs上对应的目录

```
#将对应的hive-site.xml上传
#/opt/module/hive/conf/hive-site.xml
hadoop fs -mkdir -p /home/spark_conf
hadoop fs -put /opt/module/hive/conf/hive-site.xml /home/spark_conf

useradd griffin
hadoop fs -chown -R griffin /home/spark_conf

#配置griffin-measure.jar在hdfs上的位置，spark.yarn.dist.files即为hive-site.xml上传的位置。

#配置 Griffin 的 env_batch.json
hadoop fs -mkdir -p /griffin/persist

# 配置 Griffin 的 env_streaming.json
hadoop fs -mkdir -p /griffin/checkpoint

#配置 Griffin 的measure的env-batch.json 
hadoop fs -mkdir -p /test/griffin/cp
hadoop fs -mkdir -p /griffin/streaming/dump/source
hadoop fs -mkdir -p /griffin/streaming/persist
```



### UI模块

在  `angular\src\environments\environment.ts`  中配置`前端`对应的后端服务，这个8085端口就是在上面service模块application.properties配置的端口。

```js
export const environment = {
  production: false,
  BACKEND_SERVER: 'http://192.168.28.116:8085',
};
```

`angular\src\environments\environment.prod.ts`

```js
export const environment = {
  production: true,
  BACKEND_SERVER: 'http://192.168.28.116:8085',
};
```

UI模块使用的angular

```
cd ui/angular
npm install
npm run start
```

登录，http://localhost:4200/

```
默认的用户名	user
默认的密码是 	test
```

最终结果

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618882628170-005d5ce0-5792-4bf4-a76e-2e355a232ad3.png)

#### 部署到服务器

```
npm run-script build
```

得到dist

使用nginx，安装步骤略

把dist文件夹下的打包文件拷贝到nginx/html下并重命名为 griffinUI

修改 conf/nginx.conf 文件

```
location / {
   root html/griffinUI;
   index index.html index.htm;
}
```

启动nginx

在浏览器中输入http://192.168.28.116/即可看到项目



## 启动

```
1. hdp start	启动hadoop
2. 一个单独的会话，su - esuser（不能使用su esuser命令，否则刚才配置的java环境变量在esuser用户下不会生效）
/opt/module/elasticsearch-5.6.3/bin/elasticsearch（可以在后面加 -d，即./bin/elasticsearch -d，可以后台运行）
3. /opt/module/livy/bin/livy-server start
4. nohup /opt/module/hive/bin/hive --service metastore &	必须启动，否则有一系列的问题
	hive
5. /opt/module/spark/sbin/start-all.sh

6. java -jar /opt/module/service-0.6.0.jar>service.out 2>&1 &

	tail -f /opt/module/service.out
	
	如果遇到端口占用：lsof -i :8085
	kill -9 pid
```



![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618882651664-aec7c794-0315-43d7-8009-1043cb3093cd.png)

## shell脚本

所有脚本都放在`~/bin`

```
cd ~/bin
```

### Livy

```
touch livy
vim livy
```



```
#!/bin/bash
LIVY_HOME=/opt/module/livy
case $1  in
    "start") {
            ssh tsingdata01  "${LIVY_HOME}/bin/livy-server start"
    };;
    "stop") {
            ssh tsingdata01  "${LIVY_HOME}/bin/livy-server stop"
    };;
    *){
        echo "请使用参数 start 来启动, 使用参数 stop 来关闭"
    };;
esac
```

## 问题

livy问题：Caused by: java.lang.IllegalArgumentException: Compression codec com.hadoop.compression.lzo.LzoCodec not found
https://blog.csdn.net/Lcumin/article/details/113096793



es问题：griffin.org.apache.http.conn.HttpHostConnectException: Connect to tsingdata01:9200 [tsingdata01/192.168.28.116] failed: 拒绝连接 (Connection refused)
https://blog.csdn.net/weixin_34417183/article/details/86013573

## 参考教程

[Griffin-0.6.0服务部署笔记](https://blog.csdn.net/u010834071/article/details/109843258?ops_request_misc=%7B%22request%5Fid%22%3A%22161845579616780255226649%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=161845579616780255226649&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-109843258.pc_search_result_hbase_insert&utm_term=griffin0.6)

[Apache Griffin完整安装与编译V1.0](https://blog.csdn.net/qq_37067752/article/details/107489086)