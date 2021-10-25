## List 集合

### 1、ArrayList底层源码剖析

* ArrayList底层是由数组来实现的，线程不安全的。

* 当创建ArrayList对象时，如果使用是无参构造器，则初始elementData容量为0，第一次添加则扩容elementData为10，如果再扩容，则扩容elementData为1.5

* 当创建ArrayList对象时指定大小的构造器，那么初始elementData容量就为指定的大小，如果需要扩容，则扩容elementData为1.5倍。（如：指定为8，当>8时，扩容=8 * 1.5 = 12；如果再次>12，则扩容=12*1.5=18）

> 以无参构造为例：ArrayList list1 = new ArrayList();

第1步：它会调用无参构造器

```java
public ArrayList(){this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;}
```

第2步：执行add方法

（1）执行添加操作之前，先执行modCount++;//用来记录当前集合被修改的次数，防止多线程修改出现异常

（2）首先它会去确定是否需要扩容，无参构造器，第一次传值时默认扩容为10，之后的扩容都是按照1.5倍进行扩容了

（3）扩容底层使用Arrays.copyOf 拷贝数组操作的

- Arrays.copyOf(T[] original,int newLength )：拷贝数组，其内部调用了System.arrayCopy()方法，从下标0开始，如果超过原数组长度，会用null进行填充。源码如下：

```java
  public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }
```

```java
@HotSpotIntrinsicCandidate
    public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
```

总结：看懂ArrayList源码，只要你知道底层使用数组实现的，明白grow()方法即可，其他步骤都是判断的，核心就是数组的拷贝，这样就可以减轻小白看源码的痛苦了。**浅拷贝：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝，此为浅拷贝。深拷贝：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷贝**



### 2、Vector底层源码剖析

如果是无参的，默认10，满后扩容为2倍；如果指定大小，每次直接按2倍进行扩容。底层也是可变数组，线程安全的，而ArrayList线程不安全的。

```java
// grow扩容
private Object[] grow() {
        return grow(elementCount + 1);
    }
//判断是否需要扩容
private int newCapacity(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        // 扩容算法，按照2倍进行扩容的
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        // 
        if (newCapacity - minCapacity <= 0) {
            if (minCapacity < 0) // overflow
                throw new OutOfMemoryError();
            return minCapacity;
        }
        // 再次判断新的容量是否小于最大值
        return (newCapacity - MAX_ARRAY_SIZE <= 0)
            ? newCapacity
            : hugeCapacity(minCapacity);
    }
```



### 3、LinkedList 底层源码

特点：

* LinkedList底层维护了双向链表和双端队列的，这样删除和添加效率高

* 可以添加任意元素（可以重复），包括null

* 线程不安全，没有实现同步



### 4、总结：ArrayList 和 LinkedList比较

* 修改和查询操作多，选ArrayList。增删效率低原因是底层数组扩容，改查询效率高原因是有索引
* 增删操作多，选择LinkedList。增删效率高原因是通过链表追加，改查低原因是需要一个一个地遍历
* 一般在程序中，80～90%都是查询，因此大部分使用ArrayList
* 在项目中，根据业务灵活选择，也可以这样，一个模块使用ArrayList，另一个使用LinkedList



### 5、ArrayList 和 LinkedList 区别？

底层实现不同：

ArrayList是基于数组实现，可以以O(1)时间复杂度对元素进行随机访问。删除在最坏情况是O(n)。

LinkedList底层基于链表实现的，对于删除、插入时间复杂为O(1)。

#### 6、高并发集合

线程安全：vector & Hashtable 都是线程安全的，方法被synchronized修饰

非线程安全：ArrayList & HashMap

在大量并发情况下如何提高集合的效率和安全？

使用JCU包下的工具类：

