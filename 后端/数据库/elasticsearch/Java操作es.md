



# 使用Java客户端管理ES



批量添加

```
curl -XPOST http://192.168.28.116:9200/tsingdata/test/_bulk -d '
{"index":{"_id:"3"}}
	{"name":"glong","content":"这是清数测试"}
{"index":{}}
	{"name":"lisi","content":"这是清数测试"}'
```





创建Maven项目

Maven依赖

```xml
        <dependency>
            <groupId>org.testcontainers</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>6.2.4</version>
        </dependency>
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-client</artifactId>
            <version>6.2.4</version>
        </dependency>
```

