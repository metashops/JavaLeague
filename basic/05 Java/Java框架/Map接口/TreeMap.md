### TreeSet 源码解读

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

