### SQL语句分类

* DDL：数据定义语句【】
* DML：数据操作语句【增删改查】
* DQL：数据查询语句【select】
* DCL：数据控制语句【管理数据库】



MYsql列类型

数据型（整形）的基本使用：tinyint[-128-127]、smallint[-32768-32767] 、 int

* 文本类型：Char(0-255) 、 varcher(0-65535)[0~2^16-1] 、 text(0~2^16-1) 、 longtext(0~2^32-1)

* 二进制数据类型：blob[0~2^16-1] 、 long blob[0~2^32-1]

* 日期类型：date[日期:年月日] 、time[时间:时分秒] 、 detetime[年月日时分秒YYYY-MM-DD HH:mm:ss]、timestamp[时间戳]

### 创建表

```sql
# 创建表
create table emp (
id int,
name varchar(32),
sex char(1),
brithday DATE,
entry_date DATETIME,
job varchar(32),
salary double,
resume TEXT) CHARSET utf8 COLLATE  utf8_bin ENGINE innodb;

# 插入
insert into emp values(100,'小妖怪','男','2001-11-12',
                       '2010-11-10 11;11:11','巡山的',3000,'大王叫我来巡山');
# 查询
select * from emp;
```

### 修改表

练习：

* 员工表emp的上增加一个image列，varchar类型（要求在resume后面）

  * ```sql
    ALTER TABLE emp 
    ADD image varchar(32) NOT NULL DEFAULT ''
    AFTER RESUME;#AFTER RESUME:意思就是添加在RESUME后面
    desc emp; #查看表的字段
    ```

* 修改job列，使其长度为60

  * ```sql
    ALTER TABLE emp MODIFY job varchar(60) NOT NULL DEFAULT '';
    ```

* 删除sex列

  * ```sql
    alter table emp drop sex;
    ```

* 表名修改为employee

  * ```sql
    rename table emp to employee;
    ```

* 修改表的字符集为UTF8

  * ```sql
    alter table employee character set utf8;
    ```

* 列名name修改为user_name

  * ```sql
    alter table employee change name user_name varchar(64) not null default '';
    ```

### 数据库CRUD语句

创建表

```sql
create table goods(id int,goods_name varchar(10),price double);
```

（1）Insert

添加数据：

```sql
insert into goods (id,goods_name,price) VALUES(10,'华为手机',5800);
```

（2）Update(仅删除记录，不能删除表本身)

```sql
update goods set goods_name='OPPO'  where id=10;#将ID为10的修改为goods_name='OPPO'
update goods set goods_name='小米10',price=5200 where id=30;#修改多个列
```

> update使用细节：（1）update语法可以用新值更新原有表列中的各列；（2）set子句芝士压迫修改的哪些列和要给予哪些值；（3）where子句指定要更新哪些行，没有where子句，则更新所有的行(注意使用)

（3）Delete

删除名为OPPO的记录

```sql
delete from goods where goods_name='OPPO';
```



**（4）Select（重点）**

> 基本语法：select 执行查询哪些列数据；from指定查询那张表；

单表：

```sql
# 首先创建表
create table student(
id int not null default 1,
name varchar(20) not null default '',
chinses float not null default 0.0,
english float not null default 0.0,
math float not null default 0.0
);
# 插入数据10条
insert into student(id,name,chinese,english,math) values(1,'科比',89,78,90);
```

要求：

查询表中所有学生信息

```sql
select * from student
```

查询表中所有学生的姓名和对应的英语成绩

```sql
select 'name',english from studentl
```

过滤表中重复数据distinct

```sql
select distinct * from student;
```

要查询的记录，每个字段都相同，才会去重

```sql
select distinct name from student;
select name  from student;
```

案例：

（1）统计每个学生的总分

```sql
select name,(chinese+english+math) from student;
```

（2）在所有学生总分加10分的情况

```sql
select name,(chinese+english+math+10) from student;
```

（3）使用别名表示学生分数

```sql
select name,(chinese+english+math) as total_score from student;
select name as '名字',(chinese+english+math) as total_score from student;
```

（4）在where子句中经常使用运算符

查询姓名为科比的学生成绩

```sql
select * from student where english > 90;
```

查询英语成绩大于90分的同学

```sql
select * from student where english > 90;
```

查询总分大于200分的所有同学

```sql
select * from student where (chinese+english+math) > 200;
```

查询math大于60 并且ID大于5的学生成绩

查询英语成绩大于语文成绩的同学

查询总分大于200分并且数学成绩小于语文成绩的姓科的同学；

```sql
select * from student where math > 80 and id > 5;
select * from student where math > chinese;
select * from student where (chinese+english+math) > 20 and math > chinese and name like '科%';
```

查询英语分数在80-90之间的同学

```sql
select * from student where english >= 80 and english <= 91;
select * from student where english between 80 and 91;
```

查询数学分数为89，90，91的同学

```sql
select * from student where math in (89,90,91);
```

查询所有姓du的同学成绩

```sql
select * from student where name like '杜%';
```

查询数学分大于80，语文大于80的同学

```sql
select * from student where math > 80 and chinese > 80;
```

（5）使用order by 子句排序查询结果

* Order by 指定排序的列，排序的列即可是表中的列名，也可以是select语句后指定的列名
* Asc升级（默认），Desc降级
* Order by子句应付位于select语句结尾

需求：对数学排序后输出(升序)；对总分从高到低顺序输出；对姓科的学生成绩排序输出

```sql
select * from student order by math;
select name,(chinese+english+math) as total_score from student order by total_score desc ;
# 对姓科的学生总成绩排序输出
select name,(chinese+english+math) as total_score from student where name like '科%' order by total_score desc;
```

