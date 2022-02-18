# HDFS的API操作

https://www.bilibili.com/video/BV1Qp4y1n7EN?p=46

## 在Maven项目中引入依赖坐标

```xaml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>3.1.3</version>
</dependency>
```



## 操作

```
1. 创建客户端
2. 执行操作
3. 关闭资源
```



### 创建目录

```java
public class HDFSClient {
    private FileSystem fs;

    @Test
    public void go() throws URISyntaxException, IOException, InterruptedException {
        init();
        testmkdir();
        close();
    }

    public void init() throws URISyntaxException, IOException, InterruptedException {
        // URI && 配置 && 指定user权限
        fs = FileSystem.get(new URI("hdfs://192.168.28.116:8020"), new Configuration() ,"tsingdata");
    }

    public void testmkdir() throws URISyntaxException, IOException, InterruptedException {
        fs.mkdirs(new Path("/test222"));
    }

    public void close() throws IOException {
        fs.close();
    }

}
```

### 上传

```java
fs.copyFromLocalFile();
```



```java
    // 上传
    /**
     *      参数解读:
     *      参数1:表示删除原数据
     *      参数2:是否允许覆盖
     *      参数3:原数据路径
     *      参数4: 目的地路径
     */
    public void testPut() throws IOException {
        fs.copyFromLocalFile(false, 
                    false, 
                    new Path("/Users/glong/project/vue-springboot/src/main/resources/sanguo.txt"),
                    new Path("hdfs://192.168.28.116:8020/test222"));
    }
```



### 参数优先级

```
hdfs-default.xml < hdfs-site.xml < 在项目资源目录下的配置文件(resource/hdfs-site.xml) < 代码里面的配置
```



### 下载

crc校验检测是否完全下载。

```java
	// 参数的解读:参数1:原文件是否删除;	参数2:原文件路径HDFS;	参数3:目标地址路径;	参数4:
		public void testGet() throws IOException {
        fs.copyToLocalFile(false,
                new Path("hdfs://192.168.28.116:8020/test222/sanguo.txt"),
                new Path("/Users/glong/project/vue-springboot/src/main/resources/"),
                false);
    }
```



### 删除

```java
    public void testDelete() throws IOException {
        // 1、删除文件路径   2、是否递归操作
//        fs.delete(new Path("/test222/sanguo.txt"), false);

        // 删除空目录
        fs.delete(new Path("/test222"), false);

        // 删除非空目录
//        fs.delete(new Path("/test222/"), true);
    }
```



### 文件更名和移动

```java
    public void testmv() throws IOException {
        // 修改名称，目录文件都可以
        fs.rename(new Path("/test222/sanguo.txt"), new Path("/test222/shuihu.txt"));

        // 移动
        fs.rename(new Path("/test222/sanguo.txt"), new Path("/sanguo.txt"));
    }
```

### 获取文件的详细信息

```java
public void filesDetile() throws IOException {
        // 获取所有文件信息
        RemoteIterator<LocatedFileStatus> files = fs.listFiles(new Path("/"), true);

        // 遍历
        while (files.hasNext()) {
            LocatedFileStatus fileStatus = files.next();

            System.out.println("=====" + fileStatus.getPath() + "======");

            System.out.println(fileStatus.getPermission());
            System.out.println(fileStatus.getOwner());
            System.out.println(fileStatus.getGroup());
            System.out.println(fileStatus.getLen());
            System.out.println(fileStatus.getModificationTime());
            System.out.println(fileStatus.getReplication());
            System.out.println(fileStatus.getBlockSize());
            System.out.println(fileStatus.getPath().getName());

            // 获取块信息
            BlockLocation[] blockLocations = fileStatus.getBlockLocations();
            System.out.printf(Arrays.toString(blockLocations));
        }
```

### 判断是文件还是文件夹

```java
    public void judge() throws IOException {
        FileStatus[] fileStatuses = fs.listStatus(new Path("/"));

        for (FileStatus fileStatus : fileStatuses) {
            if (fileStatus.isFile()) {
                System.out.println("这是文件" + fileStatus.getPath().getName());
            }else{
                System.out.println("这是目录" + fileStatus.getPath().getName());
            }
        }
    }
```

