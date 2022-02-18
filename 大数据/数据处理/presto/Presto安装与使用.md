# Presto安装与使用

https://repo1.maven.org/maven2/com/facebook/presto/

需要的组件

```markdown
1. presto-server	服务的
2. presto-cli		客户端
3. yanagishima		可视化
```

下载安装

![image-20210604162353663](images/image-20210604162353663.png)

```
tar -zxvf presto-server-0.196.tar.gz -C /opt/module/
mv presto-server-0.196/ presto
```



```
cd presto/
```

创建文件夹

```
mkdir data
mkdir etc
```

## 服务器端安装

进入etc

```
vim jvm.config
```

```
-server
-Xmx16G
-XX:+UseG1GC
-XX:G1HeapRegionSize=32M
-XX:+UseGCOverheadLimit
-XX:+ExplicitGCInvokesConcurrent
-XX:+HeapDumpOnOutOfMemoryError
-XX:+ExitOnOutOfMemoryError
```

Presto 可以支持多个数据源，在 Presto 里面叫 catalog，这里我们配置支持 Hive 的数据源，配置一个 Hive 的 catalog。在etc下面创建

```
mkdir catalog
vim hive.properties
```

添加如下内容

```
connector.name=hive-hadoop2
hive.metastore.uri=thrift://tsingdata01:9083
```

使用xsync分发

```
xsync presto
```

分发之后，`分别`进入三台主机的/opt/module/presto/etc的路径。配置 node 属性，node id 每个节点都不一样。

```
vim node.properties
```

```
# 第一台
node.environment=production
node.id=ffffffff-ffff-ffff-ffff-ffffffffffff
node.data-dir=/opt/module/presto/data
```

```
# 第二台
node.environment=production
node.id=ffffffff-ffff-ffff-ffff-fffffffffffe
node.data-dir=/opt/module/presto/data
```

```
# 第三台
node.environment=production
node.id=ffffffff-ffff-ffff-ffff-fffffffffffd
node.data-dir=/opt/module/presto/data
```

Presto 是由一个 coordinator 节点和多个 worker 节点组成 。 在 tsingdata01上配置成`coordinator`，在 tsingdata02、tsingdata03上配置为` worker`。

`tsingdata01`：

```
cd /opt/module/presto/etc
vim config.properties
```

添加内容如下

```
coordinator=true
node-scheduler.include-coordinator=false
http-server.http.port=8881
query.max-memory=50GB
discovery-server.enabled=true
discovery.uri=http://tsingdata01:8881
```

`tsingdata02、tsingdata03`上分别配置 worker 节点

```
cd /opt/module/presto/etc
vim config.properties
```

添加内容如下

```
coordinator=false
http-server.http.port=8881
query.max-memory=50GB
discovery.uri=http://tsingdata01:8881
```

在 tsingdata01 的/opt/module/hive 目录下，启动 Hive Metastore，用 xxxx角色

```
nohup bin/hive --service metastore >/dev/null 2>&1 &
```

分别在 tsingdata01 、tsingdata02 、tsingdata03 上启动 Presto Server

（1）前台启动 Presto，控制台显示日志

```
bin/launcher.py run
```

（2）后台启动 Presto

```
bin/launcher.py start 
```

> `注意`：如果系统下载了python3，那么很可能报错，因为Presto是用python2写的。解决方法如下：
>
> ```
> python2 bin/launcher.py start
> ```

日志查看路径/opt/module/presto/data/var/log

## 客户端安装

把jar包放到presto下

```
mv presto-cli-0.196-executable.jar prestocli
```

增加执行权限

```
chmod +x prestocli
```

启动

```
./prestocli --server tsingdata01:8881 --catalog hive --schema default
```

Presto 命令行操作

Presto 的命令行操作，相当于 Hive 命令行操作。每个表必须要加上 schema。

例如：

```
select * from schema.table limit 100;
```

## 可视化Client安装

```
unzip yanagishima-18.0.zip cd yanagishima-18.0
```



```
mv yanagishima-18.0 /opt/module/presto/
```



进入到yanagishima-18.0/conf 文件夹，编写 `yanagishima.properties` 配置

```
vim yanagishima.properties
```

```
jetty.port=7080
presto.datasources=tsingdata-presto
presto.coordinator.server.tsingdata-presto=http://tsingdata01:8881
catalog.tsingdata-presto=hive
schema.tsingdata-presto=default
sql.query.engines=presto
```

在yanagishima-18.0 路径下启动 `yanagishima`

```
nohup bin/yanagishima-start.sh >y.log 2>&1
```

http://192.168.28.116:7080/