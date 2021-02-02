---
title: 浅谈HashMap
date: 2021-01-07 20:46:26
tags: JAVA
---

# 浅谈HashMap

## 设计思想

取数组和链表的优，减少Hash的劣

#### 关于Hash算法和Hash表结构

Hash也叫做散列，哈希。基本原理就是把任意长度的输入，通过Hash算法变成固定长度的输出。

一些经典的Hash算法：MD5、SHA

Hash算法的要求：1.从Hash值不可以反向推导出原始的数据。2.输入数据的微小变化会得到完全不同的hash值，相同的数据得到相同的值。3.Hash算法的执行效率要高效，长的文本也能快速地计算出Hash值。4.Hash算法的冲突概率要小

##### 为什么会冲突?

<!--more--> 

抽屉原理，

##### Hash碰撞的解决方案

首先明确的一点是：**Hash算法是一定会有冲突的**

###### 链地址法

链表地址法是使用了一个链表数组，来存储相应的数据，当hash遇到冲突的时候依次添加到链表的后面进行处理

```java
添加一个元素的时候，首先计算元素key的Hash值，确定插入数组中的位置。如果当前位置下没有重复数据，则直接添加到当前位置。当遇到冲突的时候，添加到同一个hash值的元素后面，形成一个链表。这个链表的特点是同一个链表上的hash值相同。JDK1.8中，针对链表上的数据超过8条时，使用了红黑树进行优化。
```

JAVA中的HashMap使用的就是这种方法

###### 开放地址法

简单介绍下，之后再去学习看看

一旦发生冲突，就去寻找下一个空的 散列表地址，只要散列表足够大，空的散列地址总能找到

#### 1.7 vs 1.8

简而言之，1.7就是简单的实现了数组加链表的结构，但是存在一些问题和安全隐患，其实JDK明确说明了HashMap是线程不安全的，不适用于多线程的环境中，但是1.7的HashMap在多线程下的使用发生问题的概率很大，其中比较常见的一个问题就是死锁，造成死锁的原因在于：在修改HashMap时，可能会进行扩容操作，而1.7的扩容操作是**头插法**（看到网上都是这种说法）,在改变某一个桶中链表元素的位置时，后移动的放在了前面的前头，意思就是两个数据的位置交换了，就有可能头尾相连一直顺着查下去，造成死锁。

当然这不是JDK的锅，明明强调了不能在多线程下使用。

这个问题在1.8中得到优化，但是HashMap仍然不是线程安全的，多线程下请移步ConcurrentHashMap。



#### 常量的定义

```java
/**
     * The default initial capacity - MUST be a power of two.
     */
// 默认容量
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

/**
     * The maximum capacity, used if a higher value is implicitly specified
     * by either of the constructors with arguments.
     * MUST be a power of two <= 1<<30.
     */
// 最大容量
static final int MAXIMUM_CAPACITY = 1 << 30;

/**
     * The load factor used when none specified in constructor.
     */
// 默认负载因子
static final float DEFAULT_LOAD_FACTOR = 0.75f;

/**
     * The bin count threshold for using a tree rather than list for a
     * bin.  Bins are converted to trees when adding an element to a
     * bin with at least this many nodes. The value must be greater
     * than 2 and should be at least 8 to mesh with assumptions in
     * tree removal about conversion back to plain bins upon
     * shrinkage.
     */
// 树化阈值
static final int TREEIFY_THRESHOLD = 8;
```



## 源码学习

#### PUT方法

```java
transient Node<K,V>[] table;
 
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // 初始化桶数组table table被延迟到插入新数据时再初始化
    // 如果数组（哈希表）为null或者长度为0，则进行数组初始化操作
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 如果桶中不包含键值对节点引用，则将新键值对节点的引用存入桶中即可
    // 根据key的哈希值计算出数据插入数组的下标位置，公式为 (n-1) & hash
    if ((p = tab[i = (n - 1) & hash]) == null)
        // 如果该下标位置还没有元素，则直接创建Node对象，并插入
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        // 如果目标位置key已经存在，则直接覆盖
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        // 如果目标位置key不存在，并且节点为红黑树，则插入红黑树中
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            // 否则为链表结构，遍历链表，尾部插入
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    // 如果链表长度大于等于TREEIFY_THRESHOLD，则考虑转换为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash); // 转换为红黑树操作，内部还会判断数组长度
                    break;
                }
                // 如果链表中已经存在该key的话，直接覆盖替换
                // 条件为 true 表示当前链表包含要插入的键值对 终止遍历
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        // 判断要插入的键值对是否存在HashMap中
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            // onlyIfAbsent 表示是否仅在oldValue为null的情况下更新键值对的值
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    // 键值对数量超过阈值时，则进行扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

#### 扩容部分代码

```java
/**
     * Initializes or doubles table size.  If null, allocates in
     * accord with initial capacity target held in field threshold.
     * Otherwise, because we are using power-of-two expansion, the
     * elements from each bin must either stay at same index, or move
     * with a power of two offset in the new table.
     * 
     * @return the table
     */
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    // 如果容量大于0，意思就是容量不为空，则说明已经初始化
    if (oldCap > 0) {
        // 如果容量达到最大 (1 << 30) 则不再扩容
        // static final int MAXIMUM_CAPACITY = 1 << 30;
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 按旧容量和阈值的2倍计算新容量和阈值
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

