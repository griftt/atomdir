### ConcurrentHashMap
---------------------
        作者：Bill_Xiang_
        来源：CSDN
        原文：https://blog.csdn.net/bill_xiang_/article/details/81122044
        版权声明：本文为博主原创文章，转载请附上博文链接！
   ##### jdk 1.7 ConcurrentHashMap
  1. ######  基本信息
    ConcurrentHashMap是由Segment数组结构和HashEntry数组结构组成,ConcurrentHashMap里包含一个Segment数组，Segment的结构和HashMap类似，是一种数组和链表结构，
     一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素，
    每个Segment守护着一个HashEntry数组里的元素,当对HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁。
    （1）这是一个线程安全的类，采用分段锁原理，使用的是可重入锁 ReentranceLock 锁定部分代码。
    （2） 主要的几个类
           Segement HashEntry<k,v>
  ##### jdk 1.8的 ConcurrentHashMap

  ######  1. 基本结构
    乐观锁：使用版本号解决ABA问题,  a->b->a
    主要的数据结构还是数据加链表，同时配合volite,sysconsyize, 在链表长度大于等于8，数组个数小于64时执行红黑树转换



  2. #### 1.8的 ConcurrentHashMap 主要的方法  和属性
      sizeCtl : 为-1 表示正在初始化或正在扩容，其为0时，表示hash表还未初始化，为正数表示下次扩容的阈值，为-n表示当前有N-1个线程正在进行扩容
      new出对象时，并不会同时新建一个数组，而是在执行put操作时，做判断，如果sizeCtl=0，就执行初始化，初始化节点数组。
      （1）  map.size();  返回int，如果要返回long则使用mappingSize().
```java
      final long sumCount() {
        //DK1.8的ConcurrentHashMap中保存元素的个数的记录方法也有不同，首先在添加和删除元素时，会通过CAS操作更新ConcurrentHashMap的baseCount属性值来统计元素个数。
        但是CAS操作可能会失败，因此，ConcurrentHashMap又定义了一个CounterCell数组来记录CAS操作失败时的元素个数。因此，ConcurrentHashMap中元素的个数则通过如下方式获得：

        CounterCell[] as = counterCells;
        CounterCell a;
        long sum = baseCount;
        if (as != null) {
            for (int i = 0; i < as.length; ++i) {
                if ((a = as[i]) != null)
                    sum += a.value;
            }
        }
        return sum;
    }
```
  （2）红黑树的转换
  链表数量大于8 而且数组数量大于64时就会进行转换,同时会也仅对第一个节点上锁，元素所在的红黑树节点个数小于6，则会触发红黑树向链表结构转换
  ```java

  ```
  (3)get()
  该方法没加锁，因为node节点的value值斗志valotile,对其他线程可见。 处理主要步骤是将key的hash值进行再哈希，让hash值的高位也参与hash运算，从而减少哈希冲突。然后再查询对应的value值。
  获取最终hash值后使用tabAt(),获取value.
  问题：spread（）方法是如何让其减少哈希冲突的。

```java
tabAt(tab, (n - 1) 该方法用于找到相应的链表

public V get(Object key) {
        Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
        int h = spread(key.hashCode());
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (e = tabAt(tab, (n - 1) & h)) != null) {
            if ((eh = e.hash) == h) {
                if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                    return e.val;
            }
            else if (eh < 0)
                return (p = e.find(h, key)) != null ? p.val : null;
            while ((e = e.next) != null) {
                if (e.hash == h &&
                    ((ek = e.key) == key || (ek != null && key.equals(ek))))
                    return e.val;
            }
        }
        return null;
    }
```

  (4) put()

    1.计算key的hash值，即调用speed()方法计算hash值；
    2.获取hash值对应的Node节点位置，此时通过一个循环实现。有以下几种情况：
      如果table表为空，则首先进行初始化操作，初始化之后再次进入循环获取Node节点的位置；
      如果table不为空，但没有找到key对应的Node节点，则直接调用casTabAt()方法插入一个新节点，此时不用加锁，因为此处使用cas的乐观锁；
      如果table不为空，且key对应的Node节点也不为空，但Node头结点的hash值为MOVED(-1)，则表示需要扩容，此时调用helpTransfer()方法进行扩容；
    3.其他情况下，则直接向Node中插入一个新Node节点，此时需要对这个Node链表或红黑树通过synchronized加锁。
    4.插入元素后，判断对应的Node结构是否需要改变结构，如果需要则调用treeifyBin()方法将Node链表升级为红黑树结构；
    5.最后，调用addCount()方法记录table中元素的数量。

```java

casTabAt()：该方法用于插入一个节点，此时不用加锁

   final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {

            Node<K,V> f; int n, i, fh;
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
              //  获取链表节点
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            else if ((fh = f.hash) == MOVED)
            //  扩容
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                synchronized (f) {
                    if (tabAt(tab, i) == f) {
                        if (fh >= 0) {
                            binCount = 1;
                            for (Node<K,V> e = f;; ++binCount) {
                                K ek;
                                if (e.hash == hash &&
                                    ((ek = e.key) == key ||
                                     (ek != null && key.equals(ek)))) {
                                    oldVal = e.val;
                                    if (!onlyIfAbsent)
                                        e.val = value;
                                    break;
                                }
                                Node<K,V> pred = e;
                                if ((e = e.next) == null) {
                                    pred.next = new Node<K,V>(hash, key,
                                                              value, null);
                                    break;
                                }
                            }
                        }
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
                  //  红黑树转换
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
            }
        }
          //元素个数添加
        addCount(1L, binCount);
        return null;
    }

  ```

  (5) remove
