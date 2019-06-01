
### 简介

* HashMap基于Hash表的Map接口实现，数据存储形式是Key,Value键值对
* 允许空键和空值，Key不可以重复，Value可以重复
* 无序，也不保证顺序不会随时间变化
* 假设散列函数在桶之间正确地分散元素，基本操作（get和put)恒定性能
* 可以自动扩容
* 非线程安全，fail-fast快速失败，若有同步需求可以使用Collections.synchronizedMap 或者ConcurrentHashMap

### 工作原理

**工作原理概述**：通过hash算法，通过put和get存储和获取对象。

通过hash的方法，通过put和get存储和获取对象。存储对象时，将K/V传给put方法时，它调用hashCode计算hash从而得到bucket位置，进一步存储，HashMap会根据当前bucket的占用情况自动调整容量(超过Load Facotr则resize为原来的2倍)。获取对象时，我们将K传给get，它调用hashCode计算hash从而得到bucket位置，并进一步调用equals()方法确定键值对。如果发生碰撞的时候，Hashmap通过链表将产生碰撞冲突的元素组织起来，如果一个bucket中碰撞冲突的元素超过某个限制(默认是8)，则使用红黑树来替换链表，从而提高速度。

### 主要参数

容量(Capacity)和负载因子(Load factor)

> - **Initial capacity** The capacity is **the number of buckets** in the hash table, The initial capacity is simply the capacity at the time the hash table is created.
> - **Load factor** The load factor is **a measure of how full the hash table is allowed to get** before its capacity is automatically increased.

Capacity就是buckets的数目，Load factor就是buckets可以填充的最大比例。当bucket填充的数目（即hashmap中元素的个数）大于`capacity*load factor`时就需要调整buckets的数目为当前的2倍。

### hash函数实现方式

```
//	hash值的高位16位与低16进行XORs操作。
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

> Computes key.hashCode() and spreads (XORs) higher bits of hash to lower. Because the table uses power-of-two masking, sets of hashes that vary only in bits above the current mask will always collide. (Among known examples are sets of Float keys holding consecutive whole numbers in small tables.) So we apply a transform that spreads the impact of higher bits downward. There is a tradeoff between **speed, utility, and quality** of bit-spreading. Because many common sets of hashes are already **reasonably distributed** (so don’t benefit from spreading), and because **we use trees to handle large sets of collisions in bins**, we just XOR some shifted bits in the cheapest possible way to reduce systematic lossage, as well as to incorporate impact of the highest bits that would otherwise never be used in index calculations because of table bounds.

在设计hash函数时，因为table长度n为2的幂，在计算下标的时候，是这样实现的

```
(n - 1) & hash
```

取模算法中的除法运算效率很低，在HashMap中通过**h&（n-1）**替代取模，得到所在数组位置，效率会高很多。（前提是保证数组的容量是2的整数倍）



### 参考资料

[HashMap in Java](<https://www.geeksforgeeks.org/java-util-hashmap-in-java/>)

[HashMap工作原理及实现](<https://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/>)

[不正确地使用HashMap引发死循环及元素丢失](<<https://www.cnblogs.com/xrq730/p/5037299.html>/>)