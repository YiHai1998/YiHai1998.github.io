# Kylin



## Kylin定义

```markdown
Apache Kylin是一个开源的分布式分析引擎，提供Hadoop/Spark之上的SQL查询接口及多维分析（OLAP）能力以支持超大规模数据，最初由eBay Inc开发并贡献至开源社区。它能在亚秒内查询巨大的Hive表。
```

## Kylin特点

Kylin的主要特点包括支持SQL接口、支持超大规模数据集、亚秒级响应、可伸缩性、高吞吐率、BI工具集成等。

```markdown
1）标准SQL接口：Kylin是以标准的SQL作为对外服务的接口。

2）支持超大数据集：Kylin对于大数据的支撑能力可能是目前所有技术中最为领先的。早在2015年eBay的生产环境中就能支持百亿记录的秒级查询，之后在移动的应用场景中又有了千亿记录秒级查询的案例。

3）亚秒级响应：Kylin拥有优异的查询相应速度，这点得益于预计算，很多复杂的计算，比如连接、聚合，在离线的预计算过程中就已经完成，这大大降低了查询时刻所需的计算量，提高了响应速度。

4）可伸缩性和高吞吐率：单节点Kylin可实现每秒70个查询，还可以搭建Kylin的集群。

5）BI工具集成
    Kylin可以与现有的BI工具集成，具体包括如下内容。
    ODBC：与Tableau、Excel、PowerBI等工具集成
    JDBC：与Saiku、BIRT等Java工具集成
    RestAPI：与JavaScript、Web网页集成
    Kylin开发团队还贡献了Zepplin的插件，也可以使用Zepplin来访问Kylin服务。
```

## Kylin架构



## Kylin工作原理