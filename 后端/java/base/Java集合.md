> 当我们需要保存一组类型相同的数据的时候，我们应该是用一个容器来保存，这个容器就是数组Array，但是，使用数组存储对象具有一定的弊端，因为我们在实际开发中，存储的数据的类型是多种多样的，于是，就出现了“集合”，集合同样也是用来存储多个数据的。

集合主要有两个接口，分别为Collection< E >和Map<K,V>



## Collection



<img src="https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/source-code/dubbo/java-collection-hierarchy.png" alt="img" style="zoom:67%;" />

### List

> List，Set

#### ArrayList

#### LinkList

#### Vector

#### Stack



### Set

#### HashSet

继承自HashMap

### Queue

## Map

### HashMap



在jdk1.7，HashMap的底层数据就够就是数组+链表，首先key的hashcode会经过扰动函数（hash方法）得到hash值，再经过（n-1）&hash得到数组下标，即数据要在数组上存储的位置，这是性能更好的取模。

如果该位置为null，直接插入，如果不为null，会判断存储位置的hash值与要存储的hash值是否相同，如果相同，则覆盖，如果不相同，则出现hash碰撞，此时使用拉链法。

在jdk1.8，底层结构变为了数组+链表/红黑树，当链表的长度size达到了8（默认阈值）则将链表转换为红黑树。

ps:下图有一个小问题，来自 [issue#608](https://github.com/Snailclimb/JavaGuide/issues/608)指出：直接覆盖之后应该就会 return，不会有后续操作。参考 JDK8 HashMap.java 658 行。

![put方法](图片/put方法.png)

#### 扩容

默认数组大小是16，loadfactor为0.75（这是一种权衡，如果更大，则意味着查询速度变慢，如果更小，则意味着空间浪费），当桶大小达到12，触发2倍扩容resize。

### Hashtable

废弃

没有遵循驼峰命名。

