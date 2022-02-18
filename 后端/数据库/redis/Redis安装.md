

# Windows安装

https://github.com/tporadowski/redis/releases

# Docker安装

略

# Linux安装

下载：https://redis.io/

解压

```
cd redis-6.0.8
make
```

执行完 **make** 命令后，redis-6.0.8 的 **src** 目录下会出现编译后的 redis 服务程序 redis-server，还有用于测试的客户端程序 redis-cli：

下面启动 redis 服务：

```
cd src
./redis-server
```

`注意`这种方式启动 redis 使用的是默认配置。也可以通过启动参数告诉 redis 使用指定配置文件使用下面命令启动。

```
cd src
./redis-server ../redis.conf
```

**redis.conf** 是一个默认的配置文件。我们可以根据需要使用自己的配置文件。

启动 redis 服务进程后，就可以使用测试客户端程序 redis-cli 和 redis 服务交互了。 比如：

```
cd src
./redis-cli
```

```
redis> set name glong
OK
redis> get name
"glong"
```

