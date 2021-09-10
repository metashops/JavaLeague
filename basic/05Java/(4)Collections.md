### Collections 工具类

常用方法：

```java
class Collections_ {
    public static void main(String[] args) {
        //创建ArrayList集合
        ArrayList list = new ArrayList();
        list.add("tom");
        list.add("tom");
        list.add("tom");
        list.add("smith");
        list.add("king");
        list.add("milan");
        //reverse(list):反转List中元素的顺序
        Collections.reverse(list);
        System.out.println(list);
        //shuffle(List)：对List集合元素进行随机排序
        Collections.shuffle(list);
        System.out.println(list);
        //sort：根据元素的自然顺序排序对指定List集合元素按升序排序
        Collections.sort(list);
        System.out.println(list);
        //sort(List,Comparator)：根据指定Comparator产生的顺序对List集合元素
        Collections.sort(list, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                return ((String) o2).length() - ((String)o1).length();
            }
        });
        System.out.println(list);
        //swap(list,int,int)：指定List集合中的i处元素和j处元素进行交换
        Collections.swap(list,1,0);
        System.out.println(list);

        //frequency(Collection,Object):返回指定的集合中指定元素的出现次数
        int count = Collections.frequency(list, "tom");
        System.out.println("tom出现次数："+count);

        //copy(list dest,list src):将src中复制到dest中
        ArrayList dest = new ArrayList();
        for (int i = 0; i < list.size();i++) {
            dest.add("");
        }
        //拷贝
        Collections.copy(dest,list);
        System.out.println(dest);

        //replaceAll()使用新值替换list对象
        Collections.replaceAll(list,"tom","汤姆");
        System.out.println("替换后：" +list);
    }
}
```

