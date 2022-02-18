# 缓存

参考：

1. 不良人redis
2. [动手写分布式缓存 - GeeCache第一天 LRU 缓存淘汰策略](https://geektutu.com/post/geecache-day1.html)
   1. [Golang校招简历项目-简单的分布式缓存 - Jun10ng - 博客园 (cnblogs.com)](https://www.cnblogs.com/Jun10ng/p/12628081.html)



## 什么是缓存？

计算机内存中的一段数据

## 内存中数据特点

```markdown
1. 读写快
2. 断电即失
```



## 缓存解决了什么问题？



## LRU算法

### 核心数据结构

![implement lru algorithm with golang](https://geektutu.com/post/geecache-day1/lru.jpg)

![implement lru algorithm with golang](https://geektutu.com/post/geecache-day1/lru.jpg)

这张图很好地表示了 LRU 算法最核心的 2 个数据结构

- 绿色的是字典(map)，存储键和值的映射关系。这样根据某个键(key)查找对应的值(value)的复杂是`O(1)`，在字典中插入一条记录的复杂度也是`O(1)`。
- 红色的是双向链表(double linked list)实现的队列。将所有的值放到双向链表中，这样，当访问到某个值时，将其移动到队尾的复杂度是`O(1)`，在队尾新增一条记录以及删除一条记录的复杂度均为`O(1)`。





创建一个包含字典和双向链表的结构体类型 Cache，方便实现后续的增删查改操作。



定义了三个数据结构

`Value`是golang中的接口类型，可以理解为java中的Object类，是一个能“兜底”所有数据结构的数据类型。

`entry`是一个双向链表存储的数据结构

`Cache`则是lru核心数据结构，包含一个哈希表和一个双向链表

```go
type Value interface {

	//返回占用的内存大小
	Len() int
}

type entry struct {
	key string
	value Value
}

type Cache struct {
	//允许使用的最大内存
	maxBytes int64

	//当前已使用的内存
	nbytes int64


	ll *list.List

	cache map[string] *list.Element

	//某条记录被移除时的回调函数，可以是nil
	OnEvicted func(key string, value Value)

}
```

> 这里说一下`OnEvicted`成员，这是一个函数对象，他的作用是，在缓存中没有需要的数据对象时，我们需要去原始数据源获取，(redis中没有，就需要去数据库中获取)，但是数据源不唯一，有时候是数据库，有时候是磁盘，有时候是表格，他们的获取方式都不相同，所以`OnEvicted`成员传入的函数，就是自定义的获取方法。



## 单机并发缓存







## 一致性哈希





## 分布式节点设计





## 防止缓存击穿