# 安装

## 安装前的准备

### CentOS 取消打开文件数限制

在 `/etc/security/limits.conf`、`/etc/security/limits.d/90-nproc.conf `这2个文件的末尾加入一下内容

```bash
vim /etc/security/limits.conf
```

```
* soft nofile 65536
* hard nofile 65536
* soft nproc 131072
* hard nproc 131072
```



```bash
vim /etc/security/limits.d/20-nproc.conf
```

```
* soft nofile 65536
* hard nofile 65536
* soft nproc 131072
* hard nproc 131072
```



检查是否生效，如果是1024，说明没有生效。等后面安装完毕再重启

```
ulimit -n
```



### CentOS 取消 SELINUX

```bash
vim /etc/selinux/config
```

```
SELINUX=disabled
```



### 安装依赖

```bash
yum install -y libtool
yum install -y *unixODBC*
```



### 下载安装

```bash
yum install yum-utils

rpm --import https://repo.clickhouse.tech/CLICKHOUSE-KEY.GPG

yum-config-manager --add-repo https://repo.clickhouse.tech/rpm/stable/x86_64

yum install clickhouse-server clickhouse-client
```

### 启动

```
systemctl start clickhouse-server.service
```



客户端

```
clickhouse-client
```



```
[root@tsingdata01 clickhouse]# clickhouse-client
ClickHouse client version 21.4.7.3 (official build).
Connecting to localhost:9000 as user default.
Connected to ClickHouse server version 21.4.7 revision 54447.

tsingdata01 :) 
```



## 集群

多台机器先安装上面的步骤安装

### 修改所有机器下配置

```bash
vim /etc/clickhouse-server/config.xml
```

取消下面的注释，以让其他机器访问到

```xml
<listen_host>::</listen_host>
```



### 在三台机器的 etc目录下新建metrika.xml文件

```bash
vim /etc/metrika.xml
```

```xml
<yandex>
    <clickhouse_remote_servers>
        <perftest_3shards_1replicas>
            <shard>
                <internal_replication>true</internal_replication>
                <replica>
                    <host>tsingdata01</host>
                    <port>9000</port>
                </replica>
            </shard>
            <shard>
                <replica>
                    <internal_replication>true</internal_replication>
                    <host>tsingdata02</host>
                    <port>9000</port>
                </replica>
            </shard>
            <shard>
                <internal_replication>true</internal_replication>
                <replica>
                    <host>tsingdata03</host>
                    <port>9000</port>
                </replica>
            </shard>
        </perftest_3shards_1replicas>
    </clickhouse_remote_servers>
    <!--zookeeper相关配置-->
    <zookeeper-servers>
        <node index="1">
            <host>tsingdata01</host>
            <port>2181</port>
        </node>
        <node index="2">
            <host>tsingdata02</host>
            <port>2181</port>
        </node>
        <node index="3">
            <host>tsingdata03</host>
            <port>2181</port>
        </node>
    </zookeeper-servers>
    <macros>
        <replica>tsingdata01</replica>
    </macros>
    <networks>
        <ip>::/0</ip>
    </networks>
    <clickhouse_compression>
        <case>
            <min_part_size>10000000000</min_part_size>
            <min_part_size_ratio>0.01</min_part_size_ratio>
            <method>lz4</method>
        </case>
    </clickhouse_compression>
</yandex>
```

> 每台机器注意修改
>
>      <macros>
>       <replica>tsingdata01</replica>
>     </macros>



### 启动

启动zk

三台机器启动服务

```
systemctl start clickhouse-server.service
```



启动第一个机器的clickhouse客户端

```
clickhouse-client
```



```
select * from system.clusters
```



