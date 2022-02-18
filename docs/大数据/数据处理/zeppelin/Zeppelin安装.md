# 安装

## 源码编译安装

https://zeppelin.apache.org/docs/latest/setup/basics/how_to_build.html

参考：https://www.cnblogs.com/erlou96/p/13539696.html

### 前端（如果要汉化，需要这一步）

zepplin-web-angular

```
npm install
npm run start
```



```bash
mvn clean package -DskipTests
```

# 启动

```
bin/zeppelin-daemon.sh start
bin/zeppelin-daemon.sh stop	# 关闭
```





