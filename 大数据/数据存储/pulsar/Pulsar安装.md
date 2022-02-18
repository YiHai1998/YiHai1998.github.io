# 安装



中文文档：http://pulsar.apache.org/docs/zh-CN/standalone/



## 单机模式

下载：https://pulsar.apache.org/en/download/

或者

```
wget https://archive.apache.org/dist/pulsar/pulsar-2.7.1/apache-pulsar-2.7.1-bin.tar.gz
```

解压

```
tar xvfz apache-pulsar-2.7.1-bin.tar.gz -C /opt/module
cd /opt/module/apache-pulsar-2.7.1
```



## 启动

```
z
```



```
bin/pulsar standalone
```



## 使用

### 消费 一条消息

在 `first-subscription` 订阅中 consume 一条消息到 `my-topic` 的命令如下所示：

```bash
bin/pulsar-client consume my-topic -s "first-subscription"
```



### 生产 一条消息

向名称为 `my-topic` 的 topic 发送一条简单的消息 `hello-pulsar`，命令如下所示：

```bash
bin/pulsar-client produce my-topic --messages "hello-pulsar"
```

如果消息成功发送到 topic，则会在 `pulsar-client` 日志中出现一个确认，如下所示：

```
13:09:39.356 [main] INFO  org.apache.pulsar.client.cli.PulsarClientTool - 1 messages successfully produced
```



## 终止



