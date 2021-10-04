### 1	String的基本特性

string：字符串，使用一对双引号表示

```
string s1 = "hello";
string s2 = new string("hello");
```

String 声明为final的，不可被继承

String 实现了Serializable接口：表示字符串是支持序列化，实现了Comparable接口：表示String可以比较大小

String在JDK8及以前内部定义了final char[] value用于存储字符串数据，JDK9时改为byte[]

### 2、String的内存分配

直接使用双引号声明出来的string对象会直接存储在常量池中

```
string s1 = "hello string";
```

如果不是使用双引号声明的string对象，可以使用string提供的intern()方法

Java 6及以前，字符串常量池存放在永久代

Java7 中将字符串常量池的位置调整到Java堆中

* 所有的字符串都保存在堆中，和其他普通对象一样，这样可以让你在进行调优引用是仅需要调整堆大小就可以
* 字符串常量池概念使用的比较多，但是这个改动使得我们有足够的理由让我们重写考虑在Java7使用string.intern()。

Java 8 元空间，字符串常量在堆

可以调整堆大小

```java
-XX:MetaspaceSize=6m -XX:MaxMetaspaceSize=6m -Xms6m -Xmx6m
```

### 3、字符串拼接操作

* 常量与常量的拼接结果在常量池，原理是编译期优化
* 常量池中不会存放相同的内容常量
* 只要其中一个是变量，结果就在堆中，变量拼接的原理是stringBuilder
* 如果拼接的结果调用intern方法，则主动将常量池中还没有的字符串对象放入池中，并返回此对象

![WeChat9493e044731eb378f3ed210f11b3217e.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gtmg5xb9e1j32q612ktse.jpg)



### 4、intern()的使用

官方注解：

```java
/**
     * Returns a canonical representation for the string object.
     * <p>
     * A pool of strings, initially empty, is maintained privately by the
     * class {@code String}.
     * <p>
     * When the intern method is invoked, if the pool already contains a
     * string equal to this {@code String} object as determined by
     * the {@link #equals(Object)} method, then the string from the pool is
     * returned. Otherwise, this {@code String} object is added to the
     * pool and a reference to this {@code String} object is returned.
     * <p>
     * It follows that for any two strings {@code s} and {@code t},
     * {@code s.intern() == t.intern()} is {@code true}
     * if and only if {@code s.equals(t)} is {@code true}.
     * <p>
     * All literal strings and string-valued constant expressions are
     * interned. String literals are defined in section 3.10.5 of the
     * <cite>The Java&trade; Language Specification</cite>.
     *
     * @return  a string that has the same contents as this string, but is
     *          guaranteed to be from a pool of unique strings.
     * @jls 3.10.5 String Literals
     */
    public native String intern();
```

面试题：new String("ab")会创建几个对象？

一个对象是new关键字在堆空间创建

另一个对象是：字符串常量池中的对象，可以通过字节码指令：ldc

```java
 Code:
      stack=3, locals=2, args_size=1
         0: new           #2                  // class java/lang/String       //new在堆中
         3: dup
         4: ldc           #3                  // String ab                    //ab在常量池中
         6: invokespecial #4                  // Method java/lang/String."<init>":(Ljava/lang/String;)V   //构造器初始化
         9: astore_1                                                          //得到对象赋给str，放入局部变量表
        10: return

```

面试题：new String("a") + new String("b")会创建几个对象？在常量池不存在ab的

JDK6 以前有5个对象

JDK7 以后有4个对象

```java
public class StringTest05 {
    public static void main(String[] args) {
        //String str = new String("ab");
        //String s1 = "ab";
        //执行这行代码字符串常量池不会存在"ab"
        String str = new String("a") + new String("b");
        str.intern();//在字符串常量池中生成"ab"(JDK7没有创建常量池ab而是创建一个指向堆空间对象，JDK6创建了一个对象ab)
        String s1 = "ab";//s4变量记录的地址使用上一行的代码执行是在常量池生成的"ab"
        System.out.println(s1==str);
    }
}

```

