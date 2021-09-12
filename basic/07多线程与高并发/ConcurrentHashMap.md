> JDK11

## ConcurrentHashMap

1、ConcurrentHashMap的put()操作，直接上源码分析

```java
final V putVal(K key, V value, boolean onlyIfAbsent) {
        // 上来先判断key和value如果为空，直接抛出异常
        if (key == null || value == null) throw new NullPointerException();
        // 1.根据key值计算hashcode
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh; K fk; V fv;
            // 2.判断是否需要初始化
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();//需要扩容就进入该initTable方法
            // 3.计算出索引位置
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
       // 4.如果当前key定位出Node，如果为空表示当前可以插入数据，这里就使用CAS尝试写入，失败则自旋保证成功
                if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value)))
                    break;                   // no lock when adding to empty bin
            }
          // 5.如果当前hashCode==MOVED = -1，就执行helpTransfer进行扩容
            else if ((fh = f.hash) == MOVED)
                tab = helpTransfer(tab, f);
            else if (onlyIfAbsent // check first node without acquiring lock
                     && fh == hash
                     && ((fk = f.key) == key || (fk != null && key.equals(fk)))
                     && (fv = f.val) != null)
                return fv;
            else {
              //6、以上如何都不满足，就使用synchronized锁插入数据
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
                                    pred.next = new Node<K,V>(hash, key, value);
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
                        else if (f instanceof ReservationNode)
                            throw new IllegalStateException("Recursive update");
                    }
                }
                if (binCount != 0) {
                  // 7.如果数量大于等于8，则执行treeifyBin进行判断是否真的需要转换为红黑树
                  // 7.1转红黑树前提满足：链表长大于8且table大于64，则需要转红黑树
                  // 7.2如果链表长度大于8，但是table表小于64，就扩容。
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
            }
        }
        addCount(1L, binCount);
        return null;
    }
```

2、源码你提到CAS是什么？还有你说自旋又是什么？

CAS（Compare And Swap 比较并且替换）是乐观锁的一种实现方式，是一种轻量级锁，JUC 中很多工具类的实现就是基于 CAS 的。

CAS实现线程安全，当线程在读取数据时不进行加锁，在准备写回数据时，先去查询原值，操作的时候比较原值是否修改，若未被其他线程修改则写回，若已被修改，则重新执行读取流程。

但是会出现一下问题：

* 如果CAS操作长时间不成功的话，会导致一直自旋，相当于死循环了，CPU的压力会很大。循环时间长开销大的问题

* 出现ABA问题：ABA问题就是指假如你有一个值，我拿到这个值是1，然后我使用CAS操作去修改这个值为2，在这个过程中，假如没有一个线程更改过这个值，我是可以成功修改为2的，但是如果有有一个线程先把这个1变成了2后来又变回1，中间值更改过，这就是ABA问题。解决：加标志位，任何一个值修改，修改完成后自增+1或者使用时间戳。

* 只能保证一个共享变量原子操作的问题

3、ConcurrentHashMap的get()方法，源码：

```java
public V get(Object key) {
        Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
  // 1.根据hashCode寻找，
        int h = spread(key.hashCode());
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (e = tabAt(tab, (n - 1) & h)) != null) {
          // 2.红黑树方式获取
            if ((eh = e.hash) == h) {
                if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                    return e.val;
            }
          // 3. 不满足红黑树，就按照链表方式进行遍历
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

小结：1.8 采用红黑树之后可以保证查询效率（`O(logn)`）

