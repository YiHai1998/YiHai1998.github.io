



## 进入docker容器

```
docker exec -it 容器名 /bin/bash
docker exec -it --user root 075c04304a80 bash	# root命令进入
```



## 安装

这里用Unbutu举例，如果是Centos就不一样

```
apt-get update

apt-get install vim -y	# vim
apt install iputils-ping	# ping
```



## 复制

复制本地文件到镜像

```
docker cp 本地文件路径 容器名:/opt
```

复制镜像文件到本地

```
docker cp 容器名:/opt 本地文件路径
```

## 打包

打包，在dockerfile文件所在目录下。-f后面为指定打包到位置。

```
vim /opt/Dockerfiler
```

添加内容如下，cd到mysql-connector-java-5.1.49.jar所在目录

```
FROM apache/dolphinscheduler:latest
COPY mysql-connector-java-5.1.48.jar /opt/dolphinscheduler/lib
```



```
docker build -t apache/dolphinscheduler:mysql-driver -f /opt/Dockerfiler .
```



## 运行

```
docker run -d --name dolphinscheduler \
-e DATABASE_HOST="192.168.28.118" -e DATABASE_PORT="5432" -e DATABASE_DATABASE="dolphinscheduler" \
-e DATABASE_USERNAME="test" -e DATABASE_PASSWORD="test" \
-e ZOOKEEPER_QUORUM="192.168.28.118:2181" \
-p 12345:12345 \
apache/dolphinscheduler:mysql-driver
```



```
locate docker-compose.yml
```



