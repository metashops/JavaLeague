## Set集合

> Set接口，有3个常用子类：HashSet、TreeSet、LinkedHashSet

特点：

* 无序（添加和取出的顺序不一致），没有索引
* 不允许重复元素，所以最多包含一个null



### 1、HashSet 底层源码

HashSet底层机制是HashMap实现的，而HashMap底层是（数组+链表+红黑色），以下模拟HashMap底层（数组+链表）

```java
public class HashSetStructure {
    public static void main(String[] args) {
        //HashSet的底层是HashMap，现在模拟HashMap底层结构
        //创建一个数组
        Node[] table = new Node[16];
        //创建节点
        Node john = new Node("john", null);
        table[2] = john;
        Node jack = new Node("jcke", null);
        john.next = jack;//将jack节点挂载到John
        Node rose = new Node("Rose", null);
        jack.next = rose;
        Node lucy = new Node("lucy", null);
        rose.next = lucy;

        Node tom = new Node("tom", null);
        lucy.next = tom;
        System.out.println("table"+table);
    }
}
class Node {
    Object item;//存放数据
    Node next;//指向下一指针

    public Node(Object item, Node next) {
        this.item = item;
        this.next = next;
    }
}
```

> HashSet是如何添加的？

（1）HashSet底层是HashMap，在添加一个元素是，先得到hash值 -> 会传成 -> 索引值

（2）找到存储数据表table，看到这个索引位置是否已经存放的有元素

（3）如果没有直接加入

（4）如果有，调用equals比较，如果相同，就放弃添加，如果不同，则添加到最后。

（5）JDK 8中，如果一条链表的元素个数超过TREEIFY_THRESHOLD(默认是8)，并且table的大小 >= MIN_TREEIFY_CAPACITY(默认64)，就会进行树化(红黑色)。

Add添加方法源码：

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16（1*2*2*2*2）
static final int MAXIMUM_CAPACITY = 1 << 30;
static final float DEFAULT_LOAD_FACTOR = 0.75f;
static final int TREEIFY_THRESHOLD = 8;
static final int UNTREEIFY_THRESHOLD = 6;
static final int MIN_TREEIFY_CAPACITY = 64;
```

1、

```java
public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
```

2、执行put方法

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
```

4、putVal方法

第一次add的执行流程：

首先执行hash(key)，计算会得到哈希值，源码如下：

```java
public V put(K key, V value) {return putVal(hash(key), key, value, false, true);}
//key==null,return 0,key!=null,进行(h=key.hashCode()) ^ (h >>> 16)（key.hashCode按位移与或，无符号右移16位，避免碰撞，最终得到h,哈希值）
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

通过计算得到hash值后，执行putVal里面相关步骤

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        // 1、辅助变量
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        // 2、table是HashMap的一个数组，类型是Node[]
        // 3、if判断table是null或者0，执行resize就第一次扩容得到16空间
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        // 4、根据key的hash值去计算该key应该存放到table表那个索引位置，且把这个位置的对象，赋给辅助变量p
        // 5、再次去判断辅助变量p是否位空，有种
        // 5.1、如p为null，表示没有存放数据，则创建一个Node(存放key是你传入的值和value=PRESENT默认的)
        // 5.2、如p != null，就放在该tab[i] = newNode(hash, key, value, null);
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {// 如果添加重复元素就会进入else
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
        ++modCount;//记录修改多次
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;//返回null说明成功了
    }

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

第二次add执行流程：

1、也是先计算哈希值

2、根据key的hash值去计算该key应该存放到table表那个索引位置

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        // 第二次直接走这里，因第一次已经table已经有了
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
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

> 为什么重复不能添加进去？

```java
// 以下只截取部分代码
else {      //辅助变量
            Node<K,V> e; K k;
            //1、p.hash == hash：首先判断当前索引位置对应的链表第一个元素和准备要添加的key的hash值一样
            // 【第一个判断】
            //2、并且满足2个条件不能添加：
            //2.1准备加入的key和p指向的Node结点的key是同一个对象
            //2.2p指向的Node结点的key的equals()和准备加入的key比较厚相同
            //3、不能加入则进入第二个判断
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
           // 【第二个判断】
           //3.1 再判断p是否是红黑色
           //3.2 如果是一颗红黑色，就调用putTreeVal进行调价
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                // 【第三个判断】
                //4、如果table对应索引位置，已经是一个链表，就使用for循环比较
                //4.1依次和该链表的每一个元素比较后，则加入到该链表的最后
                //4.1.1添加到链表后，会去判断该链表是否已经达到8个节点，达到就调用treeifyBin（转红黑树）
                //4.1.2转红黑色之前，还需要判断table数组是否大于64，小于就先扩容resize
                //所以转红黑色需要满足：当前链表长度大于8，table数组的大小大于64
                //4.2依次和该链表的每一个元素比较过程中，如果有相同情况，就直接break
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
```

HashSet添加元素过程总结：

（1）添加时，首先获取元素的哈希值（底层hashCode方法）

（2）对哈希值进行运算，得出一个索引值即为要存放的哈希表中的位置号

（3）如果该位置上没有其他元素，则直接存放；如果该位置已经有其他元素，则需要进行equals判断，如果相等，则不再添加，如果不相等，则以链表的方式添加。（equals是可以程序猿自己定义的体积自己决定）

HashSet底层机制总结：

（1）HashSet底层是HashMap，第一次添加时，table数组扩容为16，临界值（threshold）是16*加载因子（loadFactor）是0.75=12

（2）如果table数组扩容使用到临界值12，就会扩容到16X2=32，新临界值就是32X0.75=24，以此类推

（3）在Java8中，如果一条链表的元素个数到达TREEIFY_THRESHOLD（默认8），并且table大小大于等于MIN_TREEIFY_CAPACITY（默认64），就会进行树化（红黑色），如果只满足大于8，仍然采用数组扩容机制。

模拟(1)(2)：

```java
//SuppressWarnings:告诉编译器忽略指定的警告，不用在编译完成后出现警告信息
@SuppressWarnings({"all"})
public class HashSetIncrement {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
        //演示（1）（2）
        for (int i = 1; i <= 100;i++) {
            hashSet.add(i);
        }
    }
}
```

模拟（3）

```java
//SuppressWarnings:告诉编译器忽略指定的警告，不用在编译完成后出现警告信息
@SuppressWarnings({"all"})
public class HashSetIncrement {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
        for (int i = 1; i <= 12; i++) {
            hashSet.add(new A(i));
        }
        System.out.println("HashSet="+hashSet);
    }
}
class A {
    private int n;
    public A(int n) {this.n = n;}
    // 重写hashCode，确保hash值相等，这样就能确保添加到同一个链表上
    @Override
    public int hashCode() {
        return 100;
    }
}
```

HashSet练习1：

定义一个Employee类，该类包含：private成员属性name和age要求：1)创建3个employee对象放入HashSet中；2)当name和age值相同，认为是相同员工，不能添加到HashSet集合中

解题：重写hashCode和equals即可实现，

```java
class EmployeeDemo {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
        hashSet.add(new Employee("milan",23));
        hashSet.add(new Employee("smith",18));
        hashSet.add(new Employee("milan",23));
        System.out.println(hashSet);
    }
}
class Employee {
    private String name;
    private int age;

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 如果name和age值相同，则返回相同的hash值
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Employee employee = (Employee) o;
        return age == employee.age && Objects.equals(name, employee.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    @Override
    public String toString() {
        final StringBuffer sb = new StringBuffer("Employee{");
        sb.append("name='").append(name).append('\'');
        sb.append(", age=").append(age);
        sb.append('}');
        return sb.toString();
    }
}
```

HashSet练习2：

定义一个Employee类，该类包含：private成员属性name、sal、birthday（MyDate类类型），其中birthday为MyDate类型（属性包括：year, month,day）,要求：

1)创建3个employee对象放入HashSet中；2)当name和birthday值相同，认为是相同员工，不能添加到HashSet集合中

```java
public class EmployeeDemo1 {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
        hashSet.add(new Employee1("aa",8900, new MyDate(2001,3,5)));
        hashSet.add(new Employee1("bb",10000, new MyDate(2008,2,9)));
        hashSet.add(new Employee1("aa",8900, new MyDate(2001,3,5)));
        hashSet.add(new Employee1("aa",8900, new MyDate(2001,3,5)));
        hashSet.add(new Employee1("aa",8900, new MyDate(2001,3,5)));
        System.out.println("HashSet="+hashSet);
    }
}
class Employee1 {
    private String name;
    private int sal;
    private MyDate birthday;

    public Employee1(String name, int sal, MyDate birthday) {
        this.name = name;
        this.sal = sal;
        this.birthday = birthday;
    }

    @Override
    public String toString() {
        final StringBuffer sb = new StringBuffer("Employee1{");
        sb.append("name='").append(name).append('\'');
        sb.append(", sal=").append(sal);
        sb.append(", birthday=").append(birthday);
        sb.append('}');
        return sb.toString();
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Employee1 employee1 = (Employee1) o;
        return Objects.equals(name, employee1.name) && Objects.equals(birthday, employee1.birthday);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, birthday);
    }
}
class MyDate {
    int year;
    int month;
    int day;

    public MyDate(int year, int month, int day) {
        this.year = year;
        this.month = month;
        this.day = day;
    }

    @Override
    public String toString() {
        final StringBuffer sb = new StringBuffer("MyDate{");
        sb.append("year=").append(year);
        sb.append(", month=").append(month);
        sb.append(", day=").append(day);
        sb.append('}');
        return sb.toString();
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        MyDate myDate = (MyDate) o;
        return year == myDate.year && month == myDate.month && day == myDate.day;
    }

    @Override
    public int hashCode() {
        return Objects.hash(year, month, day);
    }
}
```

### 2、LinkedHashSet

特点：

* LinkedHashSet是HashSet子类
* LInkedHashSet底层是一个LinkedMap，底层维护了一个数组+双向链表
* LinkedHashSet根据元素HashCode值来决定元素的存储位置，同时使用链表维护元素的次序（图），这使得元素看起来是插入顺序保存的
* LinkedHashSet不允许添加重复元素



LinkedHashSet练习：

题目：Car类（属性：name和price）如果name和price一样，则认为是相同元素，就不能添加

