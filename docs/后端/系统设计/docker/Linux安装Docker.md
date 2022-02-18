> 网站：https://docs.docker.com/engine/install/centos/

# Docker安装

卸载之前的docker

```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

安装

```sh
yum install -y yum-utils

yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
yum -y install docker-ce docker-ce-cli containerd.io
```

启动docker

```sh
systemctl start docker
systemctl restart  docker
service docker stop
```

配置镜像加速

```sh
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://ke9h1pt4.mirror.aliyuncs.com"]
}
EOF
```

重启

```sh
systemctl daemon-reload
systemctl restart docker
```

启动Docker && 设置docker开机启动

```sh
systemctl enable docker
```

## Mysql安装

```sh
docker pull mysql:5.7   # 拉取 mysql 5.7
sudo docker images
sudo docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7	
   其中 –name：容器名，此处命名为mysql
    -e：配置信息，此处配置mysql的root用户的登陆密码
    -p：端口映射，此处映射 主机3306端口 到 容器的3306端口
    -d：后台运行容器，保证在退出终端后容器继续运行
```

进入docker

```sh
docker exec -it mysql bin/bash
exit;
```



```
# 因为有目录映射，所以我们可以直接在镜像外执行
mkdir -p /mydata/mysql/conf
vim /mydata/mysql/conf/my.conf 

[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
skip-name-resolve

保存(注意评论区该配置不对，不是collection而是collation)

docker restart mysql
```

开机自启动

```
docker run --restart=always mysql:5.7
```

进入docker登录mysql

```
docker exec -it e4074a7eff88 bash
```

启动关闭实例

```
docker start 容器名或ID
docker stop
```



### 如果用服务器

要关闭防火墙，如：用的是腾讯云，要在腾讯云网站开放3306端口

```
远程访问 MySQL， 需开放默认端口号 3306.

firewall-cmd --permanent --zone=public --add-port=3306/tcp
firewall-cmd --permanent --zone=public --add-port=3306/udp

执行firewall-cmd --reload使最新的防火墙设置规则生效
```



### AlibabaCloud安装docker

[部署并使用Docker（Alibaba Cloud Linux 2） - 云服务器 ECS - 阿里云 (aliyun.com)](https://help.aliyun.com/document_detail/51853.html)



## Redis安装

pull镜像

```shell
docker pull redis
```

启动docker

```shell
mkdir -p /mydata/redis/conf
touch /mydata/redis/conf/redis.conf
echo "appendonly yes"  >> /mydata/redis/conf/redis.conf
docker run -p 6379:6379 --name redis -v /mydata/redis/data:/data -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf -d redis redis-server /etc/redis/redis.conf

```

 连接到docker的redis

```shell
docker exec -it redis redis-cli

127.0.0.1:6379> set key1 v1
OK
127.0.0.1:6379> get key1
"v1"
127.0.0.1:6379> 
```

设置redis容器在docker启动的时候启动

```shell
docker update redis --restart=always
```

## Java安装

```
yum install java-1.8.0-openjdk.x86_64
java -version

通过yum安装的默认路径为：/usr/lib/jvm
```

将jdk的安装路径加入到JAVA_HOME

```
vi /etc/profile

在文件最后加入：

#set java environment
JAVA_HOME=/usr/lib/jvm/jre-1.6.0-openjdk.x86_64
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME CLASSPATH PATH

修改/etc/profile之后让其生效
. /etc/profile （注意 . 之后应有一个空格）
```



## Ngnix

```
docker pull nginx:1.20
# 随便启动一个nginx实例，只是为了复制出配置
docker run -p 80:80 --name nginx -d nginx:1.20

cd /mydata/nginx
docker container cp nginx:/etc/nginx .
然后在外部 /mydata/nginx/nginx 有了一堆文件
mv /mydata/nginx/nginx /mydata/nginx/conf
# 停掉nginx
docker stop nginx
docker rm nginx

# 创建新的nginx
docker run -p 80:80 --name nginx \
-v /mydata/nginx/html:/usr/share/nginx/html \
-v /mydata/nginx/logs:/var/log/nginx \
-v /mydata/nginx/conf:/etc/nginx \
-d nginx:1.20

docker update nginx --restart=always


```

