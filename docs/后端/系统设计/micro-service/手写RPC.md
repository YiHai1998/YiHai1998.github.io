1. RPC远程通讯中，如何保证接口幂等性.
2. 如何手写RPC重试策略及定时补偿策略.
3. 线上服务器CPU服务器爆满?如何解决
4. 如何监控一一个Api接口丕可用.
5. IO多路复用三种不同的实现方式
6. 高并发并发的情况下如何定义、使用常量.
7. 如果所有的定时任务服务都挂了，你们怎么处理的?
8. ES与MySQL之间的数据如何实现同步呢。





## RPC原理

https://www.bilibili.com/video/BV1ns411c7jV?p=24&spm_id_from=pageDriver

>一次完整的RPC调用流程(同步调用，异步另说)如下：
>
>1. 服务消费方(client) 调用以本地调用方式调用服务;
>2.  client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体;
>3. client stub找到服务地址，并将消息发送到服务端; 
>4.  server stub收到消息后进行解码;
>5. server stub根据解码结果调用本地的服务; 
>6. 本地服务执行并将结果返回给server stub;
>7. server stub将返回结果打包成消息并发送至消费方; 
>8. client stub接收到消息，并进行解码;
>9. 服务消费方得到最终结果。
>
>RPC框架的目标就是要`2~8`这些步骤都封装起来，这些细节对用户来说是透明的，不可见的。



## Dubbo

设计

![/dev-guide/images/dubbo-framework.jpg](图片/dubbo-framework.jpg)



## BIO\NIO

### BIO

业务逻辑没有完成前，不能释放





## Netty

工作架构图

![在这里插入图片描述](图片/56127ef6c0e44ae582534a6e0d1e5d48.jpeg)

Boss Group 线程组

Worker线程组