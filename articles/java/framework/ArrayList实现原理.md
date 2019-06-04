
### 0x01.ArrayList的简介

比较常用的集合之一，实现了[Iterable](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html) <E>，[Collection](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html) <E>，[List](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) <E>，[RandomAccess](https://docs.oracle.com/javase/8/docs/api/java/util/RandomAccess.html) ，数据存储通过一个object[]，所以ArrayList相当于一个动态数组。

:gem:元素可以为空，支持随机访问，支持动态访问，支持随机插入，可序列化，动态扩容。

### 0x02.ArrayList 中元素的存储结构

```java
默认初始容量
private static final int DEFAULT_CAPACITY = 10;

一个空数组，用于无参初始化ArrayList
private static final Object[] EMPTY_ELEMENTDATA = {};

private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

存放元素的数组
transient Object[] elementData; // non-private to simplify nested class access

数组大小
private int size;
```

### 0x03.ArrayList 插入

```java

public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
}
    
private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
如果阈值（当前数组长度+1）大于数组的长度，扩容
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
验证当前的object[]数组是否为空，为空则返回默认list长度（10）和 期望最新长度minCapacity的最大值，不为空直接返回。即如果第一次初始化list传入的数组大小小于10，数组大小也是10（最小大小为10）
private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
}
```

### 0x04.扩容操作

```java

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    新数组长度等于当前数组长度右移1位+当前数组长度，即扩容0.5倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    如果扩容之后数组长度依然小于最新数组长度，直接设置为期望长度
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    如果新数组长度大于最大List长度，返回Integer.MAX_VALUE
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    最后调用Arrays.copyOf(),System.arraycopy 复制一个扩容后的新数组
    elementData = Arrays.copyOf(elementData, newCapacity);
}
 private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
}
```

### 0x05.总结

* ArrayList基于数组支持随机访问随机插入，随机访问get和随机插入set性能较好，add,remove扩容涉及到新建数组和数组copy等操作。
* add方法：验证数组长度加1之后是否会超过阈值，超过则扩容，默认增长0.5倍，即现数组长度+现数组长度右移一位
* `Arrays.copyOf，每次都需要先根据最新数组大小new一个新数组，然后调用System.arraycopy将元素复制到新数组`
* `合理设置ArrayList初始长度，减少扩容操作，以节省开销`

### 0x06.参考资料
[参考官方文档](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html/) 

**基于JDK1.8**