Atlas 官网地址：https://atlas.apache.org/

文档查看地址：[https://atlas.apache.org/2.0.0/index.html](https://atlas.apache.org/0.8.4/index.html)



# 📐安装Atlas前的环境准备

Atlas 安装分为：集成自带的 HBase + Solr；集成外部的 HBase + Solr。我这里开发中选择集成外部的 HBase + Solr，方便项目整体进行集成操作。

## 服务规划



| 服务名称     | 版本  | 功能                                        | 子服务                | 服务器      | 服务器      | 服务器      |
| ------------ | ----- | ------------------------------------------- | --------------------- | ----------- | ----------- | ----------- |
|              |       |                                             |                       | tsingdata01 | tsingdata02 | tsingdata03 |
| Hadoop——HDFS | 3.1.3 | 使用Hive的前提                              | NameNode              | √           |             |             |
|              |       |                                             | DataNode              | √           | √           | √           |
|              |       |                                             | SecondaryNameNode     |             |             | √           |
| Hadoop——Yarn | 3.1.3 |                                             | NodeManager           | √           | √           | √           |
|              |       |                                             | Resourcemanager       |             | √           |             |
|              |       |                                             |                       |             |             |             |
| Zookeeper    | 3.5.7 | Kafka的前提，存储了Broker信息和消费者信息。 | QuorumPeerMain        | √           | √           | √           |
| Kafka        | 2.4.1 | 读取元数据管理                              | Kafka                 | √           | √           | √           |
| HBase        | 2.0.5 | 存储元数据                                  | HMaster               | √           |             |             |
|              |       |                                             | HRegionServer         | √           | √           | √           |
| Solr         | 5.2.1 | 检索、查询元数据                            | Jar                   | √           | √           | √           |
| Hive         | 3.1.2 |                                             | Hive                  | √           |             |             |
| MySQL        | 5.7   | 存储Hive的元数据                            | MySQL                 | √           |             |             |
| Azkaban      | 3.8.4 | 调度                                        | AzkabanWebServer      | √           |             |             |
|              |       |                                             | AzkabanExecutorServer | √           |             |             |
| Atlas        | 2.0   |                                             | Atlas                 | √           |             |             |
| 服务数总计   |       |                                             |                       | 13          | 7           | 7           |



## JDK1.8

略

## Hadoop3.1.3

略

## Zookeeper3.5.7

### ZK集群部署

#### 解压安装

拷贝Zookeeper安装包到Linux系统下

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618907180565-0daadc7f-533a-45f7-b156-09ad581e75dd.png)

或者下载：wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.5.7/apache-zookeeper-3.5.7-bin.tar.gz

解压到指定目录

```
tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz -C /opt/module/
```

同步/opt/module/zookeeper-3.5.7目录内容到**tsingdata02**、**tsingdata03**

```
cd /opt/module
mv apache-zookeeper-3.5.7-bin/ zookeeper-3.5.7
xsync zookeeper-3.5.7/
```

#### 配置服务器编号

（1）在/opt/module/zookeeper-3.5.7/这个目录下创建zkData

```
mkdir -p zkData
```

（2）在/opt/module/zookeeper-3.5.7/zkData目录下创建一个myid的文件

```
touch myid
```



（3）编辑myid文件

注意：添加myid文件，注意一定要在linux里面创建，在notepad++等编辑器里面很可能**乱码。**

```
vim myid
```

​	在文件中添加与server对应的编号：

```
1
```

（4）拷贝配置好的zookeeper到其他机器上

```
xsync myid
```

并分别在**tsingdata02**、**tsingdata03**上修改myid文件中内容为**2**、**3**。



#### 配置修改

```
cd /opt/module/zookeeper-3.5.7/bin
vim zkEnv.sh
# 添加
JAVA_HOME="/usr/local/java"
```

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618973649437-678d25a7-e3ba-412b-8202-527eaefe4d38.png)

```
xsync zkEnv.sh
```

将/opt/module/zookeeper-3.5.7/conf这个路径下的zoo_sample.cfg修改为zoo.cfg；

```
mv zoo_sample.cfg zoo.cfg
```

打开zoo.cfg文件，修改dataDir路径：

```
vim zoo.cfg
```

修改如下内容

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618910571869-0a7643c4-a434-4624-832a-1ca2b75963c1.png)

```
dataDir=/opt/module/zookeeper-3.5.7/zkData
dataLogDir=/opt/zookeeper/logs
```

并且在末尾增加如下配置

```
#######################cluster##########################
server.1=tsingdata01:2888:3888
server.2=tsingdata02:2888:3888
server.3=tsingdata03:2888:3888
```

配置参数解读：

**server.A=B:C:D**

其中：

**A**是一个数字，表示这个是第几号服务器；

集群模式下配置一个文件myid，这个文件在dataDir目录下，这个文件里面有一个数据就是A的值，Zookeeper启动时读取此文件，拿到里面的数据与zoo.cfg里面的配置信息比较从而判断到底是哪个server。

**B**是这个服务器的ip地址；

**C**是这个服务器与集群中的Leader服务器交换信息的端口；

**D**是万一集群中的Leader服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口。



同步zoo.cfg配置文件

```
xsync zoo.cfg
```

####  操作Zookeeper

（1）启动Zookeeper

```
bin/zkServer.sh start
```

（2）查看进程是否启动

```
jps
4020 Jps
4001 QuorumPeerMain
```

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618910690164-9073de78-8c4e-411f-ae6f-6da87bd71c5f.png)

（3）查看状态：

```
bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /opt/module/zookeeper-3.5.7/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost.
```

（4）启动客户端：

```
bin/zkCli.sh
```

（5）退出客户端：

```
quit
```

（6）停止Zookeeper

```
bin/zkServer.sh stop
```



## Kafka2.4.1

### Kafka环境准备

jar包下载：http://kafka.apache.org/downloads.html

### Kafka集群部署

上传安装包到在/opt/software处

解压安装包

```
tar -zxvf kafka_2.11-2.4.1.tgz -C /opt/module/
```

修改解压后的文件名称

```
cd /opt/module
mv kafka_2.11-2.4.1/ kafka
```

在/opt/module/kafka目录下创建logs文件夹

```
mkdir logs
```

#### 配置修改

```
cd config/
vim server.properties
```

修改以下内容（重点修改下面两个图的内容）：

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618910939603-c0a9561f-3961-4291-941f-85f2e66976fa.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618911029670-bc80e172-f23a-4913-a1aa-a8c32912ab21.png)

```
#broker的全局唯一编号，不能重复
broker.id=0
#删除topic功能使能
delete.topic.enable=true
#处理网络请求的线程数量
num.network.threads=3
#用来处理磁盘IO的现成数量
num.io.threads=8
#发送套接字的缓冲区大小
socket.send.buffer.bytes=102400
#接收套接字的缓冲区大小
socket.receive.buffer.bytes=102400
#请求套接字的缓冲区大小
socket.request.max.bytes=104857600
#kafka运行日志存放的路径	
log.dirs=/opt/module/kafka/logs
#topic在当前broker上的分区个数
num.partitions=1
#用来恢复和清理data下数据的线程数量
num.recovery.threads.per.data.dir=1
#segment文件保留的最长时间，超时将被删除
log.retention.hours=168
#配置连接Zookeeper集群地址
zookeeper.connect=tsingdata01:2181,tsingdata02:2181,tsingdata03:2181
```

配置环境变量

```
vim /etc/profile
#KAFKA_HOME
export KAFKA_HOME=/opt/module/kafka
export PATH=$PATH:$KAFKA_HOME/bin
source /etc/profile
```

分发安装包

```
xsync kafka/
```

注意：分发之后记得配置其他机器的环境变量

分别在tsingdata02和tsingdata03上修改配置文件/opt/module/kafka/config/server.properties中的broker.id=1、broker.id=2

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618911278811-e8a89b1b-eedf-46a6-9993-fa9d54c5cc8b.png)

注意：broker.id不得重复

#### 启动

启动集群（脚本启动见本文档最后**脚本**这一章节）

依次在tsingdata01、tsingdata02、tsingdata03节点上启动kafka

```
bin/kafka-server-start.sh config/server.properties &
```

依次在tsingdata01、tsingdata02、tsingdata03节点上关闭kafka（脚本关闭见本文档的**脚本**这一章节）

```
bin/kafka-server-stop.sh stop
```

## HBase2.0.5

### HBase部署

上传/opt/software

解压HBase到指定目录：

```
tar -zxvf hbase-2.0.5-bin.tar.gz -C /opt/module
```

#### HBase的配置文件

修改HBase对应的配置文件。

1）/opt/module/hbase-2.0.5/conf/hbase-env.sh添加内容：

```
export JAVA_HOME=/usr/local/java
export HBASE_MANAGES_ZK=false
```

2）hbase-site.xml修改内容：



```
<configuration>
	<property>     
		<name>hbase.rootdir</name>     
		<value>hdfs://tsingdata01:8020/hbase</value>   
	</property>

     <!-- 配置启用集群分布式运行 -->
	<property>   
		<name>hbase.cluster.distributed</name>
		<value>true</value>
	</property>

   <!-- 0.98后的新变动，之前版本没有.port,默认端口为60000 -->
	<property>
		<name>hbase.master.port</name>
		<value>16000</value>
	</property>

	<property>   
		<name>hbase.zookeeper.quorum</name>
	     <value>tsingdata01,tsingdata02,tsingdata03</value>
	</property>

	<property>   
		<name>hbase.zookeeper.property.dataDir</name>
	     <value>/opt/module/zookeeper-3.5.7/zkData</value>
	</property>
  

</configuration>
```

3）配置**regionservers**，修改为

```
tsingdata01
tsingdata02
tsingdata03
```

4）软连接hadoop配置文件到hbase：

```
ln -s /opt/module/hadoop-3.1.3/etc/hadoop/core-site.xml /opt/module/hbase-2.0.5/conf/core-site.xml
ln -s /opt/module/hadoop-3.1.3/etc/hadoop/hdfs-site.xml /opt/module/hbase-2.0.5/conf/hdfs-site.xml
```

5）同步到其他服务器

```
xsync hbase-2.0.5/
```

#### 启动关闭

```
bin/start-hbase.sh
bin/stop-hbase.sh
[root@tsingdata01 bin]# jps
6160 HRegionServer
```

遇到的问题：我在启动hbase的时候，发现HMaster开启几秒后会挂掉

2021-04-22 10:12:57,420 ERROR [Thread-15] master.HMaster: ***** ABORTING master tsingdata01,16000,1619057573181: Unhandled exception. Starting shutdown. *****

java.net.ConnectException: Call From tsingdata01/192.168.157.128 to tsingdata01:9000 failed on connection exception: java.net.ConnectException: 拒绝连接; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused

解决方法：

查看日志后发现，是hbase-site.xml配置问题，hdfs的端口号为8020，而不是9000.



### 查看HBase页面

启动成功后，可以通过“host:port”的方式来访问HBase管理页面：http://192.168.157.128:16010/

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1619060461470-69cad218-e044-4761-b757-dd9bcdfd567a.png)

## Solr5.2.1

### Solr部署

1）Solr 版本要求必须是 5.2.1，见官网

2）Solr 下载：http://archive.apache.org/dist/lucene/solr/5.2.1/solr-5.2.1.tgz

3）把 solr-5.2.1.tgz 上传到 tsingdata01的/opt/software 目录下

4）解压 solr-5.2.1.tgz 到/opt/module/目录下面

```
tar -zxvf solr-5.2.1.tgz -C /opt/module/
```



5）修改 solr-5.2.1 的名称为 solr

```
mv solr-5.2.1/ solr
```

#### 配置

6）进入 solr/bin 目录，修改 solr.in.sh 文件

```
vim bin/solr.in.sh
```

添加下列指令

```
ZK_HOST="tsingdata01:2181,tsingdata02:2181,tsingdata03:2181"
SOLR_HOST="tsingdata01"
# Sets the port Solr binds to, default is 8983
# 可修改端口号
SOLR_PORT=8983
```

7）分发 Solr，进行 Cloud 模式部署

```
xsync solr
```

提示：分发完成后，分别对 **tsingdata02**、**tsingdata03**主机/opt/module/solr/bin 下的 solr.in.sh 文件，修改为 SOLR_HOST=对应主机名。

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618974627086-860c9205-c92c-4243-bcfa-6478dbda447d.png)

#### 启动

8）在三台节点上分别启动 Solr，这个就是 **Cloud** 模式

```
bin/solr start
bin/solr start
bin/solr start
[root@tsingdata01 solr]# bin/solr start
Waiting to see Solr listening on port 8983 [-]  
Started Solr server on port 8983 (pid=6563). Happy searching!
```

提示：启动 Solr 前，需要提前启动 **Zookeeper** 服务。

> 如果启动报错：Solr home directory /opt/module/solr/ must contain a solr.xml file!
>
> 那么按照下面的方式启动
>
> ```
> solr start -s /opt/module/solr/server/solr
> ```

### 查看Solr页面

9）Web 访问 8983 端口，可指定三台节点中的任意一台 IP，http://192.168.157.128:8983/

提示：UI 界面出现 **Cloud** 菜单栏时，Solr 的 Cloud 模式才算部署成功。

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618974787596-d7e653e2-de75-4ff8-b12d-683c5f992dea.png)

## Hive3.1.2

略

## Azkaban3.8.4

下载地址:http://azkaban.github.io/downloads.html

### Azkaban部署

#### 安装前准备

1) 将Azkaban Web服务器、Azkaban执行服务器、Azkaban的sql执行脚本及MySQL安装包拷贝到tsingdata01虚拟机/opt/software目录下

```
azkaban-db-3.84.4.tar.gz
azkaban-exec-server-3.84.4.tar.gz
azkaban-web-server-3.84.4.tar.gz
mysql-connector-java-5.1.48.jar
```

2) 选择**Mysql**作为Azkaban数据库，因为Azkaban建立了一些Mysql连接增强功能，以方便Azkaban设置，并增强服务可靠性。这个参考hive，将mysql-connector-java-5.1.48.jar放到server/lib下面

#### 安装

1) 在/opt/module/目录下创建azkaban目录

```
mkdir azkaban
```

2) 解压azkaban-web-server-2.5.0.tar.gz、azkaban-executor-server-2.5.0.tar.gz、azkaban-sql-script-2.5.0.tar.gz到/opt/module/azkaban目录下

```
tar -zxvf azkaban-web-server-3.84.4.tar.gz -C /opt/module/azkaban/
tar -zxvf azkaban-exec-server-3.84.4.tar.gz -C /opt/module/azkaban/
tar -zxvf azkaban-db-3.84.4.tar.gz -C /opt/module/azkaban/
```

3) 对解压后的文件重新命名

```
cd /opt/module/azkaban/
mv azkaban-web-server-3.84.4/ server
mv azkaban-exec-server-3.84.4/ executor
```

4) azkaban脚本导入

进入mysql，创建azkaban数据库，并将解压的脚本导入到azkaban数据库。

```
mysql -uroot -proot
mysql> create database azkaban;
mysql> use azkaban;
mysql> source /opt/module/azkaban/azkaban-db-3.84.4/create-all-sql-3.84.4.sql
```

注：source后跟.sql文件，用于批量处理.sql文件中的sql语句。

#### 生成密钥库

Keytool是java数据证书的管理工具，使用户能够管理自己的公/私钥对及相关证书。

```
-keystore    指定密钥库的名称及位置(产生的各类信息将不在.keystore文件中)
-genkey      在用户主目录中创建一个默认文件".keystore" 
-alias  对我们生成的.keystore 进行指认别名；如果没有默认是mykey
-keyalg  指定密钥的算法 RSA/DSA 默认是DSA
```

1）生成 keystore的密码及相应信息的密钥库

```
cd /opt/module/azkaban
keytool -keystore keystore -alias jetty -genkey -keyalg RSA
输入密钥库口令:  tsingdata
再次输入新口令:  tsingdata
您的名字与姓氏是什么?
  [Unknown]:  
您的组织单位名称是什么?
  [Unknown]:  
您的组织名称是什么?
  [Unknown]:  
您所在的城市或区域名称是什么?
  [Unknown]:  
您所在的省/市/自治区名称是什么?
  [Unknown]:  
该单位的双字母国家/地区代码是什么?
  [Unknown]:  
CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown是否正确?
  [否]:  y
 
输入 <jetty> 的密钥口令
        (如果和密钥库口令相同, 按回车):
再次输入新口令：
```

注意：

1、密钥库的密码至少必须6个字符，可以是纯数字或者字母或者数字和字母的组合等等

2、密钥库的密码最好和<jetty> 的密钥相同，方便记忆

2）将keystore 拷贝到 azkaban web服务器根目录中

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618971043800-7fe1b67c-ce9e-40c8-aded-aae7bf054481.png)

```
mv keystore /opt/module/azkaban/server/
```



### 配置文件

#### Web服务器配置

1）进入azkaban web服务器安装目录 conf目录，打开azkaban.properties文件

```
cd /opt/module/azkaban/server/conf
vim azkaban.properties
```

2）按照如下配置修改azkaban.properties文件。

```properties
# Azkaban Personalization Settings
azkaban.name=Test
azkaban.label=My Local Azkaban
azkaban.color=#FF3601
azkaban.default.servlet.path=/index
web.resource.dir=web/
default.timezone.id=Asia/Shanghai
# Azkaban UserManager class
user.manager.class=azkaban.user.XmlUserManager
user.manager.xml.file=conf/azkaban-users.xml
# Loader for projects
executor.global.properties=conf/global.properties
azkaban.project.dir=projects
# Velocity dev mode
velocity.dev.mode=false
# Azkaban Jetty server properties.
jetty.use.ssl=false
jetty.maxThreads=25
jetty.port=8081
# Azkaban Executor settings
# mail settings
mail.sender=
mail.host=
# User facing web server configurations used to construct the user facing server URLs. They are useful when there is a reverse proxy between Azkaban web servers and users.
# enduser -> myazkabanhost:443 -> proxy -> localhost:8081
# when this parameters set then these parameters are used to generate email links.
# if these parameters are not set then jetty.hostname, and jetty.port(if ssl configured jetty.ssl.port) are used.
# azkaban.webserver.external_hostname=myazkabanhost.com
# azkaban.webserver.external_ssl_port=443
# azkaban.webserver.external_port=8081
job.failure.email=
job.success.email=
lockdown.create.projects=false
cache.directory=cache
# JMX stats
jetty.connector.stats=true
executor.connector.stats=true
# Azkaban mysql settings by default. Users should configure their own username and password.
database.type=mysql
mysql.port=3306
mysql.host=tsingdata01
mysql.database=azkaban
mysql.user=root
mysql.password=root
mysql.numconnections=100
#Multiple Executor
azkaban.use.multiple.executors=true
azkaban.executorselector.filters=StaticRemainingFlowSize,CpuStatus
azkaban.executorselector.comparator.NumberOfAssignedFlowComparator=1
azkaban.executorselector.comparator.Memory=1
azkaban.executorselector.comparator.LastDispatched=1
azkaban.executorselector.comparator.CpuUsage=1
```

 3）web服务器用户配置

在azkaban web服务器安装目录 conf目录，按照如下配置修改azkaban-users.xml 文件，增加管理员用户。

vim azkaban-users.xml

```xml
<azkaban-users>
	<user username="azkaban" password="azkaban" roles="admin" groups="azkaban" />
	<user username="metrics" password="metrics" roles="metrics"/>
	<user username="tsingdata" password="12345678" roles="admin" />
	<role name="admin" permissions="ADMIN" />
	<role name="metrics" permissions="METRICS"/>
</azkaban-users>
```

#### 执行服务器配置

1）进入执行服务器安装目录conf，打开azkaban.properties

```
cd /opt/module/azkaban/executor/conf
vim azkaban.properties
```

2） 按照如下配置修改azkaban.properties文件。

```properties
# Azkaban Personalization Settings
azkaban.name=Test
azkaban.label=My Local Azkaban
azkaban.color=#FF3601
azkaban.default.servlet.path=/index
web.resource.dir=web/
default.timezone.id=Asia/Shanghai
# Azkaban UserManager class
user.manager.class=azkaban.user.XmlUserManager
user.manager.xml.file=conf/azkaban-users.xml
# Loader for projects
executor.global.properties=conf/global.properties
azkaban.project.dir=projects
# Velocity dev mode
velocity.dev.mode=false
# Azkaban Jetty server properties.
jetty.use.ssl=false
jetty.maxThreads=25
jetty.port=8081
# Where the Azkaban web server is located
azkaban.webserver.url=http://tsingdata01:8009
# mail settings
mail.sender=
mail.host=
# User facing web server configurations used to construct the user facing server URLs. They are useful when there is a reverse proxy between Azkaban web servers and users.
# enduser -> myazkabanhost:443 -> proxy -> localhost:8081
# when this parameters set then these parameters are used to generate email links.
# if these parameters are not set then jetty.hostname, and jetty.port(if ssl configured jetty.ssl.port) are used.
# azkaban.webserver.external_hostname=myazkabanhost.com
# azkaban.webserver.external_ssl_port=443
# azkaban.webserver.external_port=8081
job.failure.email=
job.success.email=
lockdown.create.projects=false
cache.directory=cache
# JMX stats
jetty.connector.stats=true
executor.connector.stats=true
# Azkaban plugin settings
azkaban.jobtype.plugin.dir=plugins/jobtypes
# Azkaban mysql settings by default. Users should configure their own username and password.
database.type=mysql
mysql.port=3306
mysql.host=tsingdata01
mysql.database=azkaban
mysql.user=root
mysql.password=root
mysql.numconnections=100
# Azkaban Executor settings
executor.maxThreads=50
executor.flow.threads=30
executor.port=123321
```

同步exec到其他服务器

```
xsync executor
```



#### 启动executor服务器

在三台服务器的executor目录下执行启动命令

```
cd /opt/module/azkaban/executor
bin/start-exec.sh
```

> 不能使用./start-exec.sh，必须有bin

在Azkaban数据库的executor表中

![image-20210616135257318](imgaes/image-20210616135257318.png)

说明启动成功

逐个激活executor

```
curl -G "tsingdata01:12321"/executor?action=activate" && echo
curl -G "tsingdata02:12321"/executor?action=activate" && echo
curl -G "tsingdata03:12321"/executor?action=activate" && echo
```

也可以直接修改executor表中的active字段的值为1.

#### 启动web服务器

在azkaban web服务器目录下执行启动命令

```
cd /opt/module/azkaban/server
bin/start-web.sh
```

注意：

先执行executor，再执行web，避免Web Server会因为找不到执行器启动失败。

遇到启动不了的情况：https://blog.csdn.net/new_Xxx/article/details/104849590

jps查看进程

```
jps
3601 AzkabanExecutorServer
5880 Jps
3661 AzkabanWebServer
```

启动完成后，在浏览器(建议使用谷歌浏览器)中输入http://192.168.28.116:8443/，即可访问azkaban服务了。

在登录中输入刚才在azkaban-users.xml文件中新添加的户用名及密码，点击 login。

# 🎾安装Atlas2.0

1、把 apache-atlas-2.0.0-server.tar.gz  上传到 tsingdata01的/opt/software 目录下

2、解压 apache-atlas-2.0.0-server.tar.gz  到/opt/module/目录下面

```
cd /opt/software
tar -zxvf apache-atlas-2.0.0-server.tar.gz -C /opt/module/
```

3、修改 apache-atlas-2.0.0 的名称为 atlas

```
cd /opt/module
mv apache-atlas-2.0.0/ atlas
```

# 💎Atlas集成外部框架

## Atlas 集成 Hbase

1）进入/opt/module/atlas/conf/目录，修改配置文件

```
vim atlas-application.properties
```



```
#修改atlas存储数据主机
atlas.graph.storage.hostname=tsingdata01:2181,tsingdata02:2181,tsingdata03:2181
```

2）进入到/opt/module/atlas/conf/hbase 路径，添加 Hbase 集群的配置文件到${Atlas_Home} 

```
ln -s /opt/module/hbase-2.0.5/conf/ /opt/module/atlas/conf/hbase/conf
```

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618987130418-87e6cb57-375b-4330-a506-9731138f8736.png)

3）在/opt/module/atlas/conf/atlas-env.sh 中添加 HBASE_CONF_DIR

```
vim atlas-env.sh
#添加 HBase 配置文件路径
export HBASE_CONF_DIR=/opt/module/atlas/conf/hbase/conf
```

## Atlas 集成 Solr

1）进入/opt/module/atlas/conf 目录，修改配置文件

```
vim atlas-application.properties
#修改如下配置
atlas.graph.index.search.solr.zookeeper-url=tsingdata01:2181,tsingdata02:2181,tsingdata03:2181
```

2）将 Atlas 自带的 Solr 文件夹拷贝到外部 Solr 集群的各个节点。

```
cp -r /opt/module/atlas/conf/solr /opt/module/solr/
```

3）进入到/opt/module/solr 路径，修改拷贝过来的配置文件名称为 atlas_conf

```
mv solr atlas_conf
```

4）在 Cloud 模式下，启动 Solr（需要提前启动 Zookeeper 集群），并创建 collection

```
cd /opt/module/solr
bin/solr create -c vertex_index -d /opt/module/solr/atlas_conf -shards 3 -replicationFactor 2 
bin/solr create -c edge_index -d /opt/module/solr/atlas_conf -shards 3 -replicationFactor 2 
bin/solr create -c fulltext_index -d /opt/module/solr/atlas_conf -shards 3 -replicationFactor 2
```

-shards 3：表示该集合分片数为 3

-replicationFactor 2：表示每个分片数都有 2 个备份

vertex_index、edge_index、fulltext_index：表示集合名称



注意：如果需要删除 vertex_index、edge_index、fulltext_index 等 collection 可以执行如下命令。

```
bin/solr delete -c vertex_index
bin/solr delete -c edge_index
bin/solr delete -c fulltext_index
```

5）验证创建 collection 成功

登录 solr web 控制台：http://192.168.157.128:8983/solr/#/~cloud 看到如下图显示：

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618987962292-a3ded092-1398-44f9-a2d5-56741be40e5d.png)

## Atlas 集成 Kafka



1）进入/opt/module/atlas/conf/目录，修改配置文件 atlas-application.properties

```
vim atlas-application.properties
```



```
#########	Notification Configs	#########

atlas.notification.embedded=false

atlas.kafka.data=/opt/module/kafka/logs

atlas.kafka.zookeeper.connect=tsingdata01:2181,tsingdata02:2181,tsingdata03:2181/kafka

atlas.kafka.bootstrap.servers=tsingdata01:9092,tsingdata02:9092,tsingdata03:9092
atlas.kafka.zookeeper.session.timeout.ms=4000 
atlas.kafka.zookeeper.connection.timeout.ms=2000

atlas.kafka.enable.auto.commit=true
```

2）启动 Kafka 集群，并创建 Topic

```
cd /opt/module/kafka
bin/kafka-topics.sh	--zookeeper tsingdata01:2181,tsingdata02:2181,tsingdata03:2181 --create --replication-factor 3 --partitions 3 --topic _HOATLASOK
bin/kafka-topics.sh	--zookeeper tsingdata01:2181,tsingdata02:2181,tsingdata03:2181 --create --replication-factor 3 --partitions 3 --topic ATLAS_ENTITIES
```



## Atlas 其他配置



1）进入/opt/module/atlas/conf/目录，修改配置文件 atlas-application.properties

```
vim atlas-application.properties
######### Server Properties ######### 
atlas.rest.address=http://tsingdata01:21000
#If enabled and set to true, this will run setup steps when the server starts
atlas.server.run.setup.on.start=false

#########	Entity Audit Configs	#########
atlas.audit.hbase.zookeeper.quorum=tsingdata01:2181,tsingdata02:2181,tsingdata03:2181
```



2）记录性能指标，进入/opt/module/atlas/conf/路径，修改当前目录下的 atlas-log4j.xml 

```
vim atlas-log4j.xml
#去掉如下代码的注释

<appender name="perf_appender" class="org.apache.log4j.DailyRollingFileAppender">
  <param name="file" value="${atlas.log.dir}/atlas_perf.log" /> <param name="datePattern" value="'.'yyyy-MM-dd" /> <param name="append" value="true" />
  <layout class="org.apache.log4j.PatternLayout">
  <param name="ConversionPattern" value="%d|%t|%m%n" /> </layout>
</appender>

<logger name="org.apache.atlas.perf" additivity="false"> <level value="debug" />
	<appender-ref ref="perf_appender" />
</logger>
```

## Atlas 集成 Hive



1）进入/opt/module/atlas/conf/目录，修改配置文件 atlas-application.properties

```
vim atlas-application.properties
######### Hive Hook Configs 直接添加#######
atlas.hook.hive.synchronous=false
atlas.hook.hive.numRetries=3
atlas.hook.hive.queueSize=10000
atlas.cluster.name=primary
```

2）解压 apache-atlas-2.0.0-hive-hook.tar.gz 到/opt/module/

```
tar -zxvf apache-atlas-2.0.0-hive-hook.tar.gz -C /opt/module/
```

3）剪切 hook 和 hook-bin 目录到到/opt/module/atlas 文件夹中

```
mv /opt/module/apache-atlas-hive-hook-2.0.0/hook-bin/ /opt/module/atlas/ 
mv /opt/module/apache-atlas-hive-hook-2.0.0/hook /opt/module/atlas/
```

4）将 atlas-application.properties 配置文件加入到 atlas-plugin-classloader-1.0.0.jar 中

```
zip -u /opt/module/atlas/hook/hive/atlas-plugin-classloader-2.0.0.jar /opt/module/atlas/conf/atlas-application.properties 
cp /opt/module/atlas/conf/atlas-application.properties /opt/module/hive/conf/
```

原因：这个配置不能参照官网，将配置文件考到 hive 的 conf 中。参考官网的做法一直读取不到 atlas-application.properties 配置文件，看了源码发现是在 classpath 读取的这个配置文件，所以将它压到 jar 里面。



5）在/opt/module/hive/conf/hive-site.xml 文件中设置 Atlas hook

```
vim hive-site.xml
<property>
  <name>hive.exec.post.hooks</name>
  <value>org.apache.atlas.hive.hook.HiveHook</value>
</property>
vim hive-env.sh
#在 tez 引擎依赖的 jar 包后面追加 hive 插件相关 jar 包
export HIVE_AUX_JARS_PATH=/opt/module/atlas/hook/hive
```

# 🚢快速启动

```
hdp start
zk.sh start
kf.sh start
hbase.sh start
solr.sh start
```

进入/opt/module/atlas 路径，重新启动 **Atlas** 服务

```
cd /opt/module/atlas
bin/atlas_start.py
```

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618994732853-c0aa2e64-ab00-4d8f-b33c-d2a845952cca.png)

提示：错误信息查看路径：/opt/module/atlas/logs/*.out 和 application.log



我遇到的问题1：虽然提示服务启动，但是无法访问http://192.168.157.128:21000

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618996560524-2cf3a66f-a46a-42a9-ade4-599736a64177.png)

然后查看application.log日志：

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1618995952576-ea31aa87-ce70-4735-8035-235223b9bf16.png)

检查后发现是atlas.graph.storage.hostname=处多添加了一些，删掉即可。



遇到的问题2：

2021-04-22 12:28:32,338 ERROR - [main:] ~ GraphBackedSearchIndexer.initialize() failed (GraphBackedSearchIndexer:307)

org.apache.solr.common.SolrException: Could not load collection from ZK: vertex_index

很明显是solr的问题，删掉collection再创建即可。详见上面Atlas集成Solr

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1619061159521-ed0aeab0-ae0c-424c-9b72-607387c89b64.png)

访问地址：http://192.168.157.128:21000

注意：等待时间大概 3 分钟。启动非常慢！！！

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1619069873360-052cdaf5-1b8e-4605-a4ec-87fd34173ebd.png)

```
账户：admin
密码：admin
```

后面我们直接使用cluster_atlas.sh脚本启动关闭整个atlas集群。

# 🔥将 Hive 元数据导入 Atlas

上面的步骤只是启动

1）配置 Hive 环境变量

```
vim /etc/profile
#配置 Hive 环境变量
export HIVE_HOME=/opt/module/hive
export PATH=$PATH:$HIVE_HOME/bin/
source /etc/profile
```

2）启动 Hive

```
hive
hive (default)> show databases;
hive (default)> use xxx;
```

3）在/opt/module/atlas/hook-bin 路径，将 Hive 元数据导入到 Atlas

```
./import-hive.sh
Using Hive configuration directory [/opt/module/hive/conf] Log file for import is /opt/module/atlas/logs/import-hive.log
log4j:WARN No such property [maxFileSize] in org.apache.log4j.PatternLayout.
log4j:WARN No such property [maxBackupIndex] in org.apache.log4j.PatternLayout.
```

输入用户名：**admin**；输入密码：**admin**

```
Enter username for atlas :- admin
Enter password for atlas :-
Hive Meta Data import was successful!!!
```

# 🚀脚本

## Hadoop启动停止脚本

略

## ZooKeeper启动停止脚本

（1）在~/bin 目录下创建脚本

```
vim zk.sh
#!/bin/bash

case $1 in
"start"){
	for i in tsingdata01 tsingdata02 tsingdata03 
	do
		ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh start"
	done		
};;
"stop"){
	for i in tsingdata01 tsingdata02 tsingdata03 
	do
		ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh stop"
	done		
};;
"status"){
	for i in tsingdata01 tsingdata02 tsingdata03 
	do
		ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh status"
	done		
};;
esac
```

（2）增加脚本执行权限

```
chmod 777 zk.sh
```

（3）zookeeper集群启动脚本

```
zk.sh start
```

（4）zookeeper集群停止脚本

```
zk.sh stop
```

## Kafka启动停止脚本

（1）在~/bin 目录下创建脚本

```
vim kf.sh
#!/bin/bash

case $1 in
"start"){
    for i in tsingdata01 tsingdata02 tsingdata03
    do
        echo " --------启动 $i Kafka-------"
        ssh $i "source /etc/profile;/opt/module/kafka/bin/kafka-server-start.sh -daemon /opt/module/kafka/config/server.properties "
    done
};;
"stop"){
    for i in tsingdata01 tsingdata02 tsingdata03
    do
        echo " --------停止 $i Kafka-------"
        ssh $i "source /etc/profile;/opt/module/kafka/bin/kafka-server-stop.sh stop"
    done
};;
esac
```

（2）增加脚本执行权限

```
chmod 777 kf.sh
```

（3）Kafka集群启动脚本

```
kf.sh start
```

（4）Kafka集群停止脚本

```
kf.sh stop
```

## HBase启动停止脚本



（1）在~/bin目录下创建脚本

```
vim hbase.sh
```

在脚本中编写如下内容

```
#!/bin/bash

case $1 in
"start"){
    for i in tsingdata01
    do
        echo " --------启动 $i hbase-------"
        ssh $i "source /etc/profile;/opt/module/hbase-2.0.5/bin/start-hbase.sh"
    done
};;
"stop"){
    for i in tsingdata01
    do
        echo " --------停止 $i hbase-------"
        ssh $i "source /etc/profile;/opt/module/hbase-2.0.5/bin/stop-hbase.sh"
    done
};;
esac
```

（2）增加脚本执行权限

```
chmod 777 hbase.sh
```

（3）hbase集群启动脚本

```
hbase.sh start
```

（4）hbase集群停止脚本

```
hbase.sh stop
```

## Solr 启动停止脚本

（1）在~/bin目录下创建脚本

```
vim solr.sh
```

在脚本中编写如下内容

```
#!/bin/bash

case $1 in
"start"){
    for i in tsingdata01 tsingdata02 tsingdata03
    do
        echo " --------启动 $i Kafka-------"
        ssh $i "source /etc/profile;/opt/module/solr/bin/solr start"
    done
};;
"stop"){
    for i in tsingdata01 tsingdata02 tsingdata03
    do
        echo " --------停止 $i Kafka-------"
        ssh $i "source /etc/profile;/opt/module/solr/bin/solr stop"
    done
};;
esac
```

（2）增加脚本执行权限

```
chmod 777 solr.sh
```

（3）Solr 集群启动脚本

```
solr.sh start
```

（4）Solr 集群停止脚本

```
solr.sh stop
```

## 集群_Atlas启动停止脚本！

（1）在~/bin目录下创建脚本

```
vim cluster_atlas.sh
```

在脚本中编写如下内容

```
#!/bin/bash

case $1 in
"start"){
        echo ================== 启动 Atlas集群 ==================

        #启动 Zookeeper集群
        zk.sh start

        #启动 Hadoop集群
        hdp start

        #启动 Kafka集群
        kf.sh start

        #启动 Hbase集群
        hb.sh start

				#启动Solr集群
        solr.sh start
        
        # 启动atlas
        /opt/module/atlas/bin/atlas_start.py

        };;
"stop"){
        echo ================== 停止 Atlas集群 ==================

        #停止 atlas
        /opt/module/atlas/bin/atlas_stop.py

        #停止 Solr集群
        solr.sh stop
        
				#停止 Hbase集群
        hb.sh stop

        #停止 Kafka采集集群
        kf.sh stop

        #停止 Hadoop集群
        hdp stop

        #停止 Zookeeper集群
        zk.sh stop

};;
esac
```

（2）增加脚本执行权限

```
chmod 777 cluster_atlas.sh
```

（3）cluster_atlas集群启动脚本

```
cluster_atlas.sh start
```

（4）Solr 集群停止脚本

```
cluster_atlas.sh stop
```