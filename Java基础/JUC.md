# JUC

## 1、ConcurrentHashMap源码

### 1.1 横向对比

----

是否线程安全	是否加锁（synchronized&volatile）	是否使用CAS
hashmap	否	否	否
hashtable	是	整个hashtable上锁	否
concurrenthashmap	是	分节点上锁	是

------------

### 1.2 JDK1.8的变化

ConcurrentHashMap从JDK1.5开始随java.util.concurrent包一起引入JDK中，在JDK8以前，ConcurrentHashMap都是基于Segment分段锁来实现的。

在JDK8以后，就换成synchronized和CAS这套实现机制。

### 1.3 JDK1.8的源码分析

```
/*
* Encodings for Node hash fields. See above for explanation.
* 重要的属性值，用于标记结点实现并发效率
*/
static final int MOVED     = -1; // hash for forwarding nodes 是否前置结点
static final int TREEBIN   = -2; // hash for roots of trees 是否TreeBin节点


/**
* map的核心内部类，基本数据包装类
* 不同于hashmap，这里使用了volatile来保持数据最新可见
*/
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    volatile V val; // 保持value最新可见
    volatile Node<K,V> next;

    Node(int hash, K key, V val, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.val = val;
        this.next = next;
    }

    public final K getKey()       { return key; }
    public final V getValue()     { return val; }
    public final int hashCode()   { return key.hashCode() ^ val.hashCode(); }
    public final String toString(){ return key + "=" + val; }
    public final V setValue(V value) { // 不能直接setValue
        throw new UnsupportedOperationException();
    }

    public final boolean equals(Object o) {
        ...
    }

    /**
         * Virtualized support for map.get(); overridden in subclasses.
         */
    Node<K,V> find(int h, Object k) {
        ...
    }
}



/*
* concurrentHashMap中的三个原子操作，对指定位置的节点进行CAS操作，控制并发安全
* 简单理解下，tabAt和setTabAt都是volatile的操作，保障对其他线程最新可见
* casTabAt，使用CAS的操作比较当前线程缓存的数据、与数组中对应结点实际的数据是否一致
*/
@SuppressWarnings("unchecked")
static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i) {
    return (Node<K,V>)U.getObjectVolatile(tab, ((long)i << ASHIFT) + ABASE);
}

static final <K,V> boolean casTabAt(Node<K,V>[] tab, int i,
                                    Node<K,V> c, Node<K,V> v) {
    return U.compareAndSwapObject(tab, ((long)i << ASHIFT) + ABASE, c, v);
}

static final <K,V> void setTabAt(Node<K,V>[] tab, int i, Node<K,V> v) {
    U.putObjectVolatile(tab, ((long)i << ASHIFT) + ABASE, v);
}



// 插入node节点方法
/** Implementation for put and putIfAbsent */
final V putVal(K key, V value, boolean onlyIfAbsent) {
    // 不允许key或value为null
    if (key == null || value == null) throw new NullPointerException();
    // 计算hash值
    int hash = spread(key.hashCode());
    int binCount = 0;
    // 死循环，何时插入成功，何时跳出
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        // 如果table为空，初始化table
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        // 根据hash值计算出在table里面的位置
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            // 如果这个位置没有值，直接放进去，不需要加锁
            if (casTabAt(tab, i, null,
                         new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        // 当遇到forward节点时，需要进行帮助扩容的操作
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
            V oldVal = null;
            // 对结点上锁而不是整个hashmap，保证并发安全，所有访问线程将会串行获取锁
            // 因为hash值相同，所以这里实际上是对hash值相同的链表的头结点上锁
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    // 根据moved判断是一个链表结点
                    if (fh >= 0) { 
                        binCount = 1;
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            // 如果hash值和value值相同，则修改对应结点的value值
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            // 如果遍历到最后一个结点，那么就证明新的节点需要插入，就把它插入到链表尾部
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    // 如果这个节点是树节点，就按照树的方式插入值
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                              value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                // 如果链表长度已经达到临界值，就需要把链表转换为树结构
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    // 将当前concurrentHashMap的元素数量加1
    addCount(1L, binCount);
    return null;
}
/* 
* 这里对多线程中的场景做更多解释：
* 
* 1、如果一个或多个线程正在对concurrenthashmap进行扩容，当前线程也要进入扩容的操作中。
* 这个扩容的操作之所以能被检测到，是因为transfer方法中在空节点上插入forward结点，
* 如果检测到需要插入的位置被forward结点占有，就帮助进行扩容。
*
* 2、如果检测到要插入的节点是非空且不是forward节点，就这个节点加锁，从而保证线程安全
*/
```

### 1.5 demo练习

```
/**
* 多线程调用concurrentHashMap，
* 尝试写入脏数据到同一个node
*/
import java.util.concurrent.ConcurrentHashMap;

public class concurrentExample extends Thread {

    ConcurrentHashMap hash = new ConcurrentHashMap();

    String name;

    public concurrentExample(String cname, ConcurrentHashMap chash){
        hash = chash;
        name = cname;
    }

    @Override
    public void run(){
        for (int i = 0; i<5; i++){
            if(hash.get(i).equals("test")){
                hash.replace(i,name);
                System.out.println("succeeded in writing "+i+"-"+name);
            }
        }
    }

    public static void main(String[] args) {
        ConcurrentHashMap chash = new ConcurrentHashMap();
        for (int i = 0; i<5; i++){
            chash.put(i,"test");
        }

        concurrentExample c1 = new concurrentExample("c1",chash);
        concurrentExample c2 = new concurrentExample("c2",chash);
        concurrentExample c3 = new concurrentExample("c3",chash);
        concurrentExample c4 = new concurrentExample("c4",chash);
        concurrentExample c5 = new concurrentExample("c5",chash);
        c1.start();
        c2.start();
        c3.start();
        c4.start();
        c5.start();
    }
}
```

## 2、JUC概览

### 2.1 concurrent概览

java专用的并发工具箱，是在多线程学习的基础之上的内容。

几个部分：

1）collections集合框架：并发容器，比如常见的concurrentHashMap

2）executor线程池

3）tools同步机制工具类

4）locks锁：重点是reentrantlock，对标synchronized

5）atomic原子操作：基础操作，构建非阻塞算法

### 2.2 同步机制的工具

1）semaphore信号量：维护一组许可证，每个acquire和release唯一对应，防止多线程进入同一个代码块

2）countdownlatch：维护一个计数器cnt, 每次调用countDown方法会让计数器的值减1，减到0的时候，那些因为调用await方法而等待的线程会被唤醒

3）cyclicBarrier：与countdownlatch类似，都是计数器维护

4）exchanger：两个线程可以互相在集合点交换对象

5）phaser：提供一种更灵活的屏障形式，可以用于控制多个线程之间的分阶段计算