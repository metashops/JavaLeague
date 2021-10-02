#### 1、Java数据类型有哪些？

有8种基本类型，其中4种整形(int,short,long,byte)、2种浮点类型(float, double)、1种字符类型char和1种boolean。

#### 2、final、finally、finalize的区别？

final

* final关键字，如果被final修饰的类，就不能再派生出新的子类，无法被继承

* 如果被final修饰，一旦被赋值后就不能修改
* 修饰方法，只能使用，不能被重写

finally

* try catch语句后面用finally，一般附带一个语句块，无论是否抛出异常都会执行finally后面的语句块，一般使用与释放资源。

finalize

finalize是Object类的一个方法，在垃圾回收器执行时会调用被回收对象的finalize()方法

#### 3、为什么String是不可变的？

String底层声明为final是不可被继承的，String在JDK8及以前内部定义了final char[] value用于存储字符串数据，JDK9时改为byte[]

#### 4、为什么string类是用final关键字修饰的，目的是什么？

* 为了实现字符串池，因为只有字符串是不可变的，字符串池才有可能实现
* 为了线程安全
* 为了确保HashCode值不变，只计算一次HashCode值然后就缓存了，不需要重新计算，提高效率

#### 5、StringBuffer 和 StringBuilder区别？

* StringBuffer是线程安全的，底层的方法被synchronized修饰的，效率低于StringBuilder
* 由于StringBuilder是线程不安全的，效率比StringBuffer更有优势，但线程不安全

#### 6、Object类有哪些方法？

* 保护方法，实现对象的浅复制，但是只有实现cloneable接口才可以调用该方法
* toString方法
* Equals 方法
* hashCode
* getClass：获取运行时类型
* finalize方法：用于释放资源
* wait方法：就是使用当前线程等待该对象的锁
* notify：该方法唤醒在该对象上等待的某个线程
* notifyall：该方法唤醒在该对象上等待的所有线程

#### 7、static关键字的作用时什么？

* 被static修饰的变量或者方法是独立于该类的任何对象，这些变量和方法**不属于任何一个实例对象，而是被类的实例对象所共享**
* static修饰方法：不需要创建对象，直接使用“类名.方法名”的方式调用
* static修饰代码块：只会在类加载的时候执行一次，可用于初始化等操作

#### 8、“==” 和 “equals”区别？

（1）“==”

* 如果是基本数据类型，则直接对值进行比较
* 如果是引用数据类型，则是对地址进行比较

（2）“equals”

* 如果两个对象的类型一致，并且内容一致，则返回true
* 大多数情况下equals会重写，按照重写的规则进行比较，一般重写equals还会重写hashcode的

#### 9、为什么重写equals还要重写hashcode，不写会怎么样？

hashcode相等，equals不一定相等，但是如果equals相同，hashcode一定相同。比如map，存放的时候先进比较hashcode然后再比较equals，如果只重写的话，对象比较的时候会出错。

#### 10、内部类有哪些？

内部类主要有4种：静态内部类、成员内部类、局部内部类、匿名内部类。（很重要，很多底层大量出现）

（1）局部内部类

```java
class Outer1 {
    public String name = "张三";
    private int age = 100;
    public void f1() {
        //局部内部类
        class Inner1 {
            public void f1() {
                System.out.println("我是局部内部类，我调用类外部类age="+age);
            }
        }
        Inner1 inner1 = new Inner1();
        inner1.f1();
    }
}
```

总结：

* 局部内部类是定义在外部类的局部位置，通常在方法
* 不能添加访问修饰符，但是可以使用final修饰
* 作用域：仅在定义的方法或代码块中
* 局部内部类可以直接访问外部类
* 外部类只能在方法中创建局部内部类的实例对象

（2）匿名内部类

```java
public class AnonymousInnerDemo2 {
    public static void main(String[] args) {
        Outer2 outer2 = new Outer2();
        outer2.method();
    }
}
//外部类
class Outer2 {
    private int num = 100;
    public void method() {
        /**
         * 基于接口的匿名内部类：
         * 传统的实现接口，我们需要写一个实现该接口的类，并创建对象
         * 但是有些类我们只用一次，所以引入匿名内部类
         */
        A tiger = new A() {
            @Override
            public void cry() {
                System.out.println(" you are yyds....");
            }
        };tiger.cry();
        B b = new B("tom") {
            @Override
            public void test() {
                System.out.println("匿名内部类重写了test方法");
            }
        };
        b.test();
        System.out.println(b.getClass());
    }
}
interface A {
    public void cry();
}
class B {
    public B (String name) {
    }
    public void test() {
        System.out.println("~~~诸葛亮~~~");
    }
}
```

实战匿名内部类：

```java
package com.cathax.javabasic;

public class AnonymousInnerDemo3 {
    public static void main(String[] args) {
        //当做参数直接传递，简洁高效
        AI ai = new AI();
        ai.test(new IL() {
            @Override
            public void show() {
                System.out.println("累不累。。。");
            }
        });
    }     
}
class AI {
    //静态方法，形参是接口
    public void test(IL il) {
        il.show();
    }
}
//接口
interface IL {
    void show();
}
```

总结：

* 本质是类，但是该类没有名字
* 匿名内部类是定义在外部类的局部位置，比如方法，并且没有类名
* 匿名内部类适合创建那种只需要使用一次的类