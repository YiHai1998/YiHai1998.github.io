# Flume内存优化

1）问题描述：如果启动消费 Flume 抛出如下异常

```markdown
ERROR hdfs.HDFSEventSink: process failed
java.lang.OutOfMemoryError: GC overhead limit exceeded 
```

2）解决方案步骤：

（1）在 glong01服务器的/opt/module/flume/conf/flume-env.sh 文件中增加如下配置

 ```
 export JAVA_OPTS="-Xms100m -Xmx2000m -Dcom.sun.management.jmxremote"
 ```

（2）同步配置到 glong02，glong03服务器

```
xsync flume-env.sh
```

3）Flume 内存参数设置及优化

```markdown
1. JVM heap 一般设置为 4G 或更高
2. -Xmx 与-Xms 最好设置一致，减少内存抖动带来的性能影响，如果设置不一致容易导致频繁 fullgc。
3. -Xms 表示 JVM Heap(堆内存)最小尺寸，初始分配；-Xmx 表示 JVM Heap(堆内存)最大允许的尺寸，按需分配。如果不设置一致，容易在初始化时，由于内存不够，频繁触发 fullgc。
```



