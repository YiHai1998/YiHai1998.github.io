Spark分区源码阅读：

textfile -> hadoopfile -> hadooprdd -> getpartition -> getsplit -> fileinputformat

调用next -> readline读取文件，一读就是读的一行文件



分区步骤：

![1637153775231](C:\Users\SHENHAI\AppData\Local\Temp\1637153775231.png)