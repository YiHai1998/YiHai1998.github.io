# 安装

[安装教程](https://blog.csdn.net/qq_33845894/article/details/115354581?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-4&spm=1001.2101.3001.4242)

官网：https://dolphinscheduler.apache.org/zh-cn/index.html

## 使用Docker安装

按官网来即可：https://dolphinscheduler.apache.org/zh-cn/docs/latest/user_doc/docker-deployment.html



## 配置MySQL数据源

1. 下载 MySQL 驱动包 [mysql-connector-java-5.1.49.jar](https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.49/mysql-connector-java-5.1.49.jar) (要求 `>=5.1.47`)
2. 创建一个新的 `Dockerfile`，用于添加 MySQL 驱动包:

```
vim /opt/Dockerfile
```

```
FROM apache/dolphinscheduler:latest
COPY mysql-connector-java-5.1.49.jar /opt/dolphinscheduler/lib
```

>mysql-connector-java-5.1.49.jar和Dockerfile一个目录下，如果不这样会带上整个根目录，或者创建.dockerignore也可以。

1. 构建一个包含 MySQL 驱动包的新镜像:

```
docker build -t apache/dolphinscheduler:mysql-driver -f /opt/Dockerfile.
```

> -f可以不加，" . "一定要加到末尾

1. 将 `docker-compose.yml` 文件中的所有 `image` 字段修改为 `apache/dolphinscheduler:mysql-driver`

```
vim /opt/module/apache-dolphinscheduler-1.3.6-src/docker/docker-swarm/docker-compose.yml
```

> 如果你想在 Docker Swarm 上部署 dolphinscheduler，你需要修改 `docker-stack.yml`

关闭删除之前的容器

```
cd apache-dolphinscheduler-1.3.6-src/docker/docker-swarm
docker-compose up -d
```

访问：http://192.168.28.118:12345/dolphinscheduler/

在数据源中心创建数据源。

登陆

```
psql -U root dolphinscheduler
root
```

资源文件上传

```sql
select * from t_ds_resources;
```

```
/dolphinscheduler/root/resources
```

# 问题

dolphinscheduler的python需要调用本地资源，如果同时有很多python在运行，可能出现随机杀死，此时我们无法再找到
