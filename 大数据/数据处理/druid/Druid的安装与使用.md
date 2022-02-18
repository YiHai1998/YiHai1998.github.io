# Druid安装与使用

下载：https://imply.io/get-started

说明：imply集成了Druid，提供了Druid从部署到配置到各种可视化工具的完整的解决方案

解压安装

```
tar -zxvf imply-2021.05-1.tar -C /opt/module/
```

```
mv imply-2021.05-1/ imply
```



```
cd /opt/module/imply/conf/druid/_common
```

```
vim common.runtime.properties
```

修改Druid的ZK配置

```
druid.zk.service.host=tsingdata01:2181,tsingdata02:2181,tsingdata03:2181
```

修改启动命令参数，使其不校验不启动内置ZK

```
cd /opt/module/imply/conf/supervise
```

```
vim quickstart.conf
```

```
:verify bin/verify-java
#:verify bin/verify-default-ports
#:verify bin/verify-version-check
:kill-timeout 10

#!p10 zk bin/run-zk conf-quickstart
```

启动zookeeper

启动imply

```
bin/supervise  -c conf/supervise/quickstart.conf
```

>说明：每启动一个服务均会打印出一条日志。可以通过/opt/module/imply-2.7.10/var/sv/查看服务启动时的日志信息

访问：http://192.168.28.116:9095/

