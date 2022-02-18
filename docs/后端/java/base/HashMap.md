# HashMap

## jdk1.7

在jdk1.8之前，底层数据结构是通过数组+链表实现的，当我们创建hashmap时会先创建一个数组，当我们用`put方法`存数据时，先根据key的hashcode值计算出hash值，然后用这个哈希值确定在数组中的位置，也就是桶下标，再把value值放进去，如果这个位置本来没放东西，就会直接放进去，如果之前就有，就会生成一个链表，把新放入的值放在头部，当用`get方法`取值时，会先根据key的hashcode值计算出hash值，确定位置，再根据equals方法从该位置上的链表中取出该value值，当容量超过当前容量的0.75倍之后，就会自动扩容为原来容量的2倍。这个0.75就是负载因子。

```ruby
hash值有扰动函数hash(hashcode)得到
桶下标由hash&(n-1)得到，这是一种高性能的取模与运算
阈值0.75根据泊松分布得到，为了平衡时间与空间
```

## 扩容

扩容的话，hash值是确定的，但是因为取模需要与运算，hash&(n-1)，最后的桶下标也会发生变化。

### jdk1.7扩容时的并发死链

要在 JDK 7 下运行，否则扩容机制和 hash 的计算方法都变了

以下测试代码是精心准备的，不要随便改动

```java
public static void main(String[] args) {
    // 测试 java 7 中哪些数字的 hash 结果相等
    System.out.println("长度为16时，桶下标为1的key");
    for (int i = 0; i < 64; i++) {
        if (hash(i) % 16 == 1) {
            System.out.println(i);
        }
    }
    System.out.println("长度为32时，桶下标为1的key");
    for (int i = 0; i < 64; i++) {
        if (hash(i) % 32 == 1) {
            System.out.println(i);
        }
    }
    // 1, 35, 16, 50 当大小为16时，它们在一个桶内
    final HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
    // 放 12 个元素
    map.put(2, null);
    map.put(3, null);
    map.put(4, null);
    map.put(5, null);
    map.put(6, null);
    map.put(7, null);
    map.put(8, null);
    map.put(9, null);
    map.put(10, null);
    map.put(16, null);
    map.put(35, null);
    map.put(1, null);
    System.out.println("扩容前大小[main]:" + map.size());
    new Thread() {
        @Override
        public void run() {
            // 放第 13 个元素, 发生扩容
            map.put(50, null);
            System.out.println("扩容后大小[Thread-0]:" + map.size());
        }
    }.start();
    new Thread() {
        @Override
        public void run() {
            // 放第 13 个元素, 发生扩容
            map.put(50, null);
            System.out.println("扩容后大小[Thread-1]:" + map.size());
        }
    }.start();
}

final static int hash(Object k) {
    int h = 0;
    if (0 != h && k instanceof String) {
        return sun.misc.Hashing.stringHash32((String) k);
    }
    h ^= k.hashCode();
    h ^= (h >>> 20) ^ (h >>> 12);
    return h ^ (h >>> 7) ^ (h >>> 4);
}
```



## jdk1.8

但是在jdk1.8之后，HashMap 进行了一些修改，最大的不同就是利用了红黑树，所以其由 数组+链表+ 红黑树组成。因为在1.7的时候，这个链表的长度不固定，所以如果key的hashcode重复之后，那么对应的链表的数据的长度就无法控制了，get数据的时间复杂度就取决于链表的长度了，为了提高这一部分的性能，加入了红黑树，如果链表的长度超过8位之后，会将链表转换为红黑树，极大的降低了时间复杂度。

## 非线程安全

HashMap 线程不安全，即任一时刻可以有多个线程同时 写 HashMap，可能会导致数据的不一致。如果需要满足线程安全，可以用 Collections 的 synchronizedMap 方法使 HashMap 具有线程安全的能力，或者使用 ConcurrentHashMap。



