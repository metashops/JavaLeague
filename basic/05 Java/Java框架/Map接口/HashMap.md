#### HashMap底层机制

* HashMap底层维护了Node类型的数组table，默认null
* 当创建对象时，将加载因子（loadfactor）初始化为0.75。
* 当添加Key-value时，通过Key的哈希值得到在table的索引，然后判断该索引处是否有元素，如果没有元素之间添加。如果该索引处有元素，继续判断该元素的Key是否和准备加入的Key相等，如果相等，则直接替换value；如果不相等需要判断是树结构还是链表结构，做出相应处理。如果添加时发现容量不够，则需要扩容。
* 第一次添加，则需要扩容table容量为16，临界值（threshold）为12。
* 之后的添加，则需要扩容table容量为原来的2倍，临界值为原来的2倍
* 在Java8中，如果一条链表的元素超过TREEIFY_THRESHOLD(默认8)，并且table的大小>=MIN_TREEIFY_CAPACITY(默认62)，就会进行树化（红黑树）。



源码解读：

1、先执行构造器和初始化加载因子loadfactor为0.75，以及初始化table为null（注：table类型是HashMap$Node[] table = null）

2、计算Hash值

首先hash方法，计算Key的hash值，hash直接计算如下源码：

```java
public V put(K key, V value) {//key:"java" value:10
        return putVal(hash(key), key, value, false, true);
    }
static final int hash(Object key) {
        int h;
        //如果key=null直接返回0，否则通过hashcode的再[按位异或]，在按无符号右移16这样就得到了hash值
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

3、执行putVal() 方法

第一次就会判断table为空则，进行初始化默认大小为16，临界值12，然后通过hash得到索引位置，如果为空就直接挂上去。



```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;//辅助变量
       //第一次添加时，只看以下1开头即可表示：
       //1.0 先判断table为null直接执行resize方法进行扩容（resize就是对table扩容的，源码我也贴在下面了）
       //1.1 resize主要是扩容第一次就将table大小默认为16，临界值为12（0.75*16=12）
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        //1.2 通过hash值得到在table表的索引位置，为null就直把K-v创建一个Node加入该位置
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        //1.3 记录+1
        ++modCount;
        //1.4 再判断当前的大小：>临界值就resize()扩容；否则就挂起成功了
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

resize方法源码：

* table类型：是Node的数组类型（Node<k,v>[]类型）
* Table初始默认大小为16，临界值为12。

```java
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
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
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];//table第一次创建核心代码就这了
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



Key相同，value不同会出现覆盖，其实就是hash冲突问题

hash值相同有三种情况，源码剖析如下：

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        // 1 当hash值不为null，也就是说hash值和前面的相同，此时有以下三种情况：(1)(2)(3)
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;//辅助变量
            //(1)如果table的索引位置key的hash相同和新的key的hash值相同，并且满足
            //table原先的key和新添加的Key是同一个对象 ||(或者) equals内容相同，返回真
            // 那么就将覆盖,然后退出
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //(2)如果已经是红黑树，就按照红黑树方式处理
            //这里处理的思路：如果是红黑树（该位置的hash相同但是，但是后面是个链表，
            //那么for循环：将你传入hash值相同的进行一个一个比较，如果都不相同，直接挂在最后面即可）
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                //(3)如果找到的结点，后面是链表，就循环比较
                for (int binCount = 0; ; ++binCount) {//死循环
                    if ((e = p.next) == null) {
                      //(3.1)如果整个链表，没有一个相同的，就添加到该链表后面
                        p.next = newNode(hash, key, value, null);
           //(3.1.1)添加进去后，判断是否需要树化，当链表到达8个后，就调用treeifyBin进行红黑色(有条件)
           //treeifyBin里面的任务：
           //第一：判断table<64就进行扩容，>64就进行红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    //(3.2)循环发现有相同hash，equals也相同就退出
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;//替换新的值
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        //如果当前size大于临界值就进行扩容
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

treeifyBin方法：

```java
final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        //如果table为null或者大小还是小于64，暂时不进行红黑树，而是扩容
       //如果table不为null并且大小 >= 64，就进行红黑树
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            resize();
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            TreeNode<K,V> hd = null, tl = null;
            do {
                TreeNode<K,V> p = replacementTreeNode(e, null);
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }
```



模型HashMap触发扩容，以及转化红黑色的情况

```java
public class HashMapSource2 {
    public static void main(String[] args) {
        HashMap hashMap = new HashMap();
        for (int i = 1; i <= 12; i++) {
            hashMap.put(new A(i),"hello");
        }
        System.out.println("hashMap="+hashMap);
    }
}
class A {
    private int num;
    public A(int num) {
        this.num = num;
    }
    @Override
    public int hashCode() {
        return 100;//固定hashCode值（所有A对象的hashCode值都是100）方便模拟
    }
    @Override
    public String toString() {
        return "\nA{" +
                "num=" + num +
                "}";
    }
}
```



#### 总结

在开发中，选择什么集合实现类，主要取决于你的业务操作特点：

（1）先判断存储的类型（一组对象或者一组键值对）

（2）一组对象：Collection接口

* 允许重复：List
  * 增删多：LinkedList（底层维护了一个双向链表）
  * 改查多：ArrayList（底层维护Object类型的可变书桌）
* 不允许重复：Set
  * 无序：HashSet（底层是HashMap，维护了一个哈希表，即数组+链表+红黑树）
  * 排序：TreeSet
  * 插入和取出顺序一致：LinkedHashSet，底层维护数组+双向链表

（3）一组键值对：Map

* 键无序：HashMap【底层：哈希表JDK7:数组+链表；JDK8:数组+链表+红黑树】
* 键排序：TreeMap
* 键插入和取出顺序一致：LinkedHashMap
* 读取文件：properties


