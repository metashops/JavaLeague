## Map集合

Map接口有5个常见实现子类：HashMap、Hashtable、LinkedHashMap、TreeMap、Properties

JDK 8 为例，特点：

* Map用于保存具有映射关系的数据：Key-Value
* Map中的Key和Value可以时任何引用类型的数据，会封装到HashMap$Node对象中。
* Map中的Key不允许重复，原因和HashSet一样
* Map中的Value可以重复
* Map中的Key可以为null，value也可以为null，注意Key为null只能有一个，value为null可以多个
* 常用String类作为Map的Key
* Key和value之间存在单向一对一关系，即通过指定的Key总能找到对应的value
* Map存储数据的Key-value示意图，一对k-v是存放在一个Node中的，因为Node实现了Entry接口，



### 1、HashMap

#### （1）Put()方法解读：

k-v为了方便遍历，会创建EntrySet集合，该集合存放的元素的类型Entry，而一个Entry对象就有k,v EntrySet<Entry<k,v>>

#### （2）Map 遍历

```java
public class MapSource_ {
    public static void main(String[] args) {
        Map map = new HashMap<>();
        map.put("no1","曹操");
        map.put("no2","刘备");
        // 第一组：先取出所有Key，通过Key取出对应的Value
        Set keySet = map.entrySet();
        //(1) 增强for
        System.out.println("-----第一种遍历-----");
        for (Object key :keySet) {
            System.out.println(key+" " + map.get(key));
        }
        // 迭代器
        System.out.println("-----第二种遍历-----");
        Iterator iterator = keySet.iterator();
        while (iterator.hasNext()) {
            Object next = iterator.next();
            System.out.println(next);
        }
        // 第二组：把所有value取出
        Collection values = map.values();
        //这里可以使用所有的Collections使用的遍历方式
        //(1)增强for
        for (Object value :values) {
            System.out.println(value);
        }
        System.out.println("---取出所有的value迭代器-----");
        //(2)迭代器
        Iterator iterator1 = values.iterator();
        while (iterator1.hasNext()) {
            Object next = iterator1.next();
            System.out.println(next);
        }
        //第三组：通过EntrySet来获取k-v
        Set entrySet = map.entrySet();
        //(1)增强for
        System.out.println("----使用EntrySet的增强for---");
        for (Object entry :entrySet) {
            //将entry转成map.entry
            Map.Entry m = (Map.Entry)entry;
            System.out.println(m.getKey()+"-"+m.getValue());
        }
        //(2)iterator
        Iterator iterator2 = entrySet.iterator();
        while (iterator2.hasNext()) {
            Object entry = iterator2.next();
            Map.Entry m = (Map.Entry)entry;
            System.out.println(m.getKey()+"-"+m.getValue());
            //System.out.println(next.getClass());
        }
    }
}
```

栗子：

使用HashMap添加3个员工对象，要求

* 键：员工ID
* 值：员工对象
* 遍历显示工资>18000的员工（遍历至少使用两种）
* 员工类：姓名、工资、员工ID

源码：

```java
@SuppressWarnings({"all"})
public class MapExercise {
    public static void main(String[] args) {
        Map hashMap = new HashMap();
        hashMap.put(1,new Emp("jck",200000,1));
        hashMap.put(2,new Emp("tom",21000,2));
        hashMap.put(3,new Emp("milan",12000,3));
        // 遍历2种
        //1、使用KeySet
        Set keySet = hashMap.keySet();
        for (Object key: keySet) {
            //先获取value
            Emp emp = (Emp) hashMap.get(key);
            if (emp.getSal() > 18000) {
                System.out.println(emp);
            }
        }
        //2、使用EntrySet的
        Set entrySet = hashMap.entrySet();
        Iterator iterator = entrySet.iterator();
        while (iterator.hasNext()) {
            Map.Entry entry = (Map.Entry) iterator.next();
            //通过entry获取Key和value
            Emp emp = (Emp) entry.getValue();
            if (emp.getSal() > 18000) {
                System.out.println(emp);
            }

        }
    }
}

class Emp {
    private String name;
    private double sal;
    private int id;

    public Emp(String name, double sal, int id) {
        this.name = name;
        this.sal = sal;
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getSal() {
        return sal;
    }

    public void setSal(double sal) {
        this.sal = sal;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Emp emp = (Emp) o;
        return Double.compare(emp.sal, sal) == 0 && id == emp.id && Objects.equals(name, emp.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, sal, id);
    }

    @Override
    public String toString() {
        final StringBuffer sb = new StringBuffer("Emp{");
        sb.append("name='").append(name).append('\'');
        sb.append(", sal=").append(sal);
        sb.append(", id=").append(id);
        sb.append('}');
        return sb.toString();
    }
}
```

小结：

1. Map接口是常用实现类：HashMap、Hashtable、Properties
2. HashMap是Map接口使用频率最高的实现类
3. HashMap是以Key-value对的方式存储数据（其实就是HashMap$Node类）
4. Key不能重复，但值可以重复，允许使用null键和null值
5. 如果添加相同的Key，则会覆盖原来的Key-value，等同于修改（Key不会被替换，value会）
6. 与HashSet一样，不保证映射的顺序，因为底层是以Hash表的方式存储的（JDK8中HashMap底层：数组+链表+红黑色）
7. HashMap没有实现同步，因此是线程不安全的

#### （3）HashMap底层机制

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



### 2、Hashtable

* 存放元素是键值对
* Hashtable的键值都不能为null，否则会抛出空指针异常
* Hashtable是线程安全的，HashMap是线程不安全的

#### 3 、properties

* 继承Hashtable的，所以很多特性都是一样
* 有相同的键也是替换的



#### 4、总结

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



### 5、TreeSet 源码解读

（1）当使用无参构造器，创建Treeset时，是无法实现无序的

（2）假如现在需要添加元素，按照字符串大小排序

（3）使用TreeSet提供的一个构造器，可以传入一个比较器（匿名内部类）

（4）并且可以指定排序

```java
public class TreeSet_ {
    public static void main(String[] args) {
        TreeSet treeSet = new TreeSet(new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                return ((String) o2).compareTo((String) o1);
                //return ((String) o2).length() - ((String) o1).length();
            }
        });
        treeSet.add("ff");
        treeSet.add("aa");
        treeSet.add("bb");
        treeSet.add("bb");
        treeSet.add("cc");
        treeSet.add("ddd");
        System.out.println(treeSet);

    }
}

```

#### 6、TreeMap

（1）默认使用构造器是无序的

（2）需要使用TreeMap提供饿构造器

（3）现要求：按照传入的k(String) 的大小进行排序

```java
public class TreeMap_ {
    public static void main(String[] args) {
        TreeMap treeMap = new TreeMap(new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                //按照传入的k(String) 的大小进行排序
                //return ((String) o2).compareTo((String) o1);
                //按照字符串长度大小排序
                return ((String) o2).length()-((String) o1).length();
            }
        });
        treeMap.put("jack","杰克");
        treeMap.put("tome","汤姆");
        treeMap.put("Kristina","克瑞斯提诺");
        treeMap.put("smith","斯密丝");
        System.out.println(treeMap);
    }
}
```

源码：

```java
// 1. 构造器：把传入的实现comparator接口匿名内部类（对象）传给TreeMap的Comparator
public TreeMap(Comparator<? super K> comparator) {//comparator匿名内部类
        this.comparator = comparator;
    }
```

```java
// 第一次添加，把K-v封装到Entry对象，放入root
Entry<K,V> t = root;
        if (t == null) {//第一次添加走第一个if，直接添加
            compare(key, key); // type (and possibly null) check

            root = new Entry<>(key, value, null);
            size = 1;
            modCount++;
            return null;
        }
```



```java
 public V put(K key, V value) {
        Entry<K,V> t = root;
        if (t == null) {//第一次添加走第一个if，直接添加
            compare(key, key); // type (and possibly null) check

            root = new Entry<>(key, value, null);
            size = 1;
            modCount++;
            return null;
        }
        //第二次添加
        int cmp;
        Entry<K,V> parent;
        // split comparator and comparable paths
        Comparator<? super K> cpr = comparator;
        if (cpr != null) {
            do {//遍历所有的Key
                parent = t;
                cmp = cpr.compare(key, t.key);//动态绑定到我们的匿名内部类的compare
                if (cmp < 0)
                    t = t.left;
                else if (cmp > 0)
                    t = t.right;
                else//如果遍历过程中，发现准备添加Key和当前已有的Key相等，就不添加
                    return t.setValue(value);
            } while (t != null);
        }
        else {
            if (key == null)
                throw new NullPointerException();
            @SuppressWarnings("unchecked")
                Comparable<? super K> k = (Comparable<? super K>) key;
            do {
                parent = t;
                cmp = k.compareTo(t.key);
                if (cmp < 0)
                    t = t.left;
                else if (cmp > 0)
                    t = t.right;
                else
                    return t.setValue(value);
            } while (t != null);
        }
        Entry<K,V> e = new Entry<>(key, value, parent);
        if (cmp < 0)
            parent.left = e;
        else
            parent.right = e;
        fixAfterInsertion(e);
        size++;
        modCount++;
        return null;
    }
```

