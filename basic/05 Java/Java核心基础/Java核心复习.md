#### static关键字：

作用：

* 第一，为某特定数据类型或对象分配单一的存储空间，而与创建对象的个数无关。

* 第二，实现某个方法或属性与类而不是对象关联在一起

使用：

（1）成员变量

使用 static 关键字可以达到全局的效果，静态变量属于类的。

```java
public static int t1 = 0;
```

（2）成员方法

Static 方法属于类的方法，不需要创建对象就可调用，static方法中不能使用this和super关键字，不能调用非static方法，以下简单的单例模式

```java
public class Test {
    private volatile static Test INSTANCE = null;
    private Test(){}
    public static Test getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new Test();
        }
        return INSTANCE;
    }
    public static void main(String[] args) {
        Test instance1 = Test.getInstance();
        Test instance2 = Test.getInstance();
        System.out.println(instance1 == instance2);
    }
}
```

（3）代码块

静态代码块，如果有多个，那么JVM加载类时会按照顺序执行。我们常用来初始化静态变量，**静态代码块只会执行一次**。

```java
public class Test {
    static {
        System.out.println(" 我是 static 代码块！");
    }
    public static void main(String[] args) {
    }
}
// 运行结果： 我是 static 代码块！
```

（4）内部类

static内部类是指声明为static的内部类，静态内部类只能访问外部类的静态成员和静态方法（包括私有）

```java
public class Test {
    private static String a = "我是外部私有的静态成员变量";
    private int b = 5;
    static class Test1 {
        void fun() {
            System.out.println(a);
            //System.out.println(b);//不可访问非static的成员变量及方法
        }
    }
    public static void main(String[] args) {
        Test1 test1 = new Test1();
        test1.fun(); // 运行结果：我是外部私有的静态成员变量
    }
}
```

