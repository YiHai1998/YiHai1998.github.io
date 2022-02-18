# Griffin使用



## ElasticSearch存储数据

在service模块，MetricStoreImpl.java里面，持久化层会查询数据

```
http://192.168.28.116:9200/griffin/_search/?filter_path=hits.hits._source
```

