# 日志采集 Flume 安装

项目经验之 Flume 组件选型

```markdown
1. Source
	（1）Taildir Source 相比 Exec Source、Spooling Directory Source 的优势TailDir Source：断点续传、多目录。Flume1.6 以前需要自己自定义 Source 记录每次读取文件位置，实现断点续传。
	Exec Source 可以实时搜集数据，但是在 Flume 不运行或者 Shell 命令出错的情况下，数据将会丢失。
	Spooling Directory Source 监控目录，支持断点续传。

	（2）batchSize 大小如何设置？
	答：Event 1K 左右时，500-1000 合适（默认为 100）

2. Channel
	采用 Kafka Channel，省去了 Sink，提高了效率。KafkaChannel 数据存储在 Kafka 里面，所以数据是存储在磁盘中。

	注意在 Flume1.7 以前，Kafka Channel 很少有人使用，因为发现 parseAsFlumeEvent 这个配置起不了作用。也就是无论 parseAsFlumeEvent 配置为 true 还是 false，都会转为 Flume Event。这样的话，造成的结果是，会始终都把 Flume 的 headers 中的信息混合着内容一起写入 Kafka 的消息中，这显然不是我所需要的，我只是需要把内容写入即可。
```

日志采集 Flume 配置

```

1）Flume 配置分析
```

