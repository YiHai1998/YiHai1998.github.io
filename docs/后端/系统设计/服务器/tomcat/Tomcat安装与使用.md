# 安装

下载tomcat并部署：

https://tomcat.apache.org/download-80.cgi下载tar.gz的linux版本

```
wget https://mirrors.bfsu.edu.cn/apache/tomcat/tomcat-8/v8.5.66/bin/apache-tomcat-8.5.66.tar.gz
```

解压后，将war包拷贝`apache-tomcat-8.5.66/webapps`目录下



修改端口，原本是8080，容易冲突

```
conf/server.xml
```



启动

```
./startup.sh
```

关闭

```
./shutdown.sh
```

