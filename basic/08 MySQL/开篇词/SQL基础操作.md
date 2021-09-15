## SQL语句分类

* DDL：数据定义语句
* DML：数据操作语句【增删改查】
* DQL：数据查询语句【select】
* DCL：数据控制语句【管理数据库】



## MYsql列类型

数据型（整形）的基本使用：tinyint[-128-127]、smallint[-32768-32767] 、 int

* 文本类型：Char(0-255) 、 varcher(0-65535)[0~2^16-1] 、 text(0~2^16-1) 、 longtext(0~2^32-1)

* 二进制数据类型：blob[0~2^16-1] 、 long blob[0~2^32-1]

* 日期类型：date[日期:年月日] 、time[时间:时分秒] 、 datetime[年月日时分秒YYYY-MM-DD HH:mm:ss]、timestamp[时间戳]

## 创建表

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

# 插入(大家多插入多条记录，方便后面练习)
insert into emp values(100,'小妖怪','男','2001-11-12',
                       '2010-11-10 11;11:11','巡山的',3000,'大王叫我来巡山');
# 查询
select * from emp;
```

## 修改表

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

## 数据库CRUD语句

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



## 统计函数

1、count函数

要求：

（1）统计一个班共有多少人

```sql
select count(*) from student;
```

（2）统计数学成绩大于90分的学生有多少个

```sql
select count(*) from student where math > 90;
```



（3）统计总分大于250分的人数有多少

```sql
select count(*) from studnet where (chinese + english + math) > 250;
```

2、sum函数-求和（只对数字有作用）

需求：

（1）统计一个班及数学总分数

```sql
select sum(math) from student;
```

（2）统计该班级语文、英语、数学各科的总成绩

```sql
select sum(math),sum(english),sum(chinese) from student;
```

（3）统计该班级的语文、英语、数学成绩的总和

```sql
select sum(math+english+chinese) from sutdent;
```

（4）统计该班级语文成绩平均分

```sql
select sum(chinese) / count(*) from student;
```

3、avg函数-求平均值

需求：

（1）数学的平均分

```sql
select avg(math) from student;
```

（2）求一个班级总分平均值

```sql
select avg(math+english+chinese) from studnet;
```

4、Max/min-求最大值/最小值

需求：

（1）求该班级的总分最高分和总分最低分

```sql
select Max(math+english+chiese),min(math+english+chinese) from student;
```

（2）求该班级数学最高分和最低分

```sql
select max(math) as 'math_high_socre',min(math) as 'math_low_socre' from student;
```



## 分组统计

创建表

```sql
/*部分表*/
create table dept(
  deptno mediumint unsigned not null default 0,
  dname varchar(20) not null detault "",
  loc varchar(14) not null detault ""
);
insert into dept values(10,'ACCOUNTING','NEW YURK'),(20,'RESEARCH','DALLAS'),
(30,'SALES','CHICHGO'),(40,'OPERATIONS','BOSTON')

/*雇员表*/
create table emp(
  empno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,/*编号*/
  ename varchar(20) NOT NULL DEFAULT "",/*名字*/
  job varchar(9) NOT NULL DEFAULT "",/*工作*/
  mgr MEDIUMINT UNSIGNED,/*上级编号*/
  hiredate DATA NOT NULL,/*入职时间*/
  sal DECIMAL(7,2) NOT NULL,/*薪水*/
  comm DECIMAL(7,2),/*红利*/
  deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0
);
/*工资级别表*/
create table salgrade(
grade MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
  losal DECIMAL(17,2) NOT NULL,
  hisal DECIMAL(17,2) NOT NULL
);
INSERT INTO salgrade values (1,700,1200);
INSERT INTO salgrade values (2,2300,1400);
INSERT INTO salgrade values (3,1500,2000);
INSERT INTO salgrade values (4,3001,3000);
INSERT INTO salgrade values (5,3005,9900);
```

使用group by子句队列进行分组，使用having子句队分组后的结果进行过滤

要求：

（1）如何显示每个部门的平均工资和最高工资？

```sql
select avg(sal),max(sal),deptno from emp GROUP BY deptno;
```

（2）显示每个部门的每种岗位的平均工资和最低工资？

```sql
select avg(sal),min(sal),deptno,job from emp GROUP BY deptno,job;
```

（3）显示平均工资低于2000的部门号和它的平均工资？

```sql
select avg(sal),deptno from emp GROUP BY deptno HAVING avg(sal) < 5000;
select avg(sal) as avg_sal,deptno from emp GROUP BY deptno HAVING avg_sal < 5000;
```



## 日期函数

我们先来新建一张表，用来测试日期函数的

```sql
create table mes (id int,content varchar(30),send_time DATETIME);
insert into mes values(2,"科比",CURRENT_TIMESTAMP());
```

当前日期：CURRENT_DATE

当前时间：COURRENT_TIME

当前时间戳：COURRENT_TIMESTAMP

返回datetime的日期部分：DATE(datetime)

在date2上加上一个时间：DATE_ADD(date2,INTERVAL d_value d_type)

在date2上减去一个时间：DATE_SUB(date2,INTERVAL d_value d_type)

date1和date2时间差，结果是天：DATEDIFF(date1,date2)

当前时间：NOW()

**案例：需求如下**

（1）显示所有留言信息，发布日期只显示日期，不用显示时间

```sql
select id ,content,date(send_time) from mes;/*只显示年月日*/
select id ,content,time(send_time) from mes;/*只显示时间*/
```

（2）轻查询在10分钟内发布的帖子

```sql
select * from mes where date_add(send_time,INTERVAL 10 MINUTE) >= NOW();
```

（3）请在mysql的SQL语句中求出2011-11-11 和 1990-1-1 相差多少天

```sql
select DATEDIFF('2011-11-11','1990-01-01') from DUAL
```

（4）请用mysql的SQL语句求出你活了多少天

（5）如果你能活80岁，求出你还能活多少天

（6）FROM_UNIXTIME：可以吧一个UNIX_TIMESTAMP秒数，转成指定格式日期。`%Y-%m-%d`格式是规定好的，表示年月日

```sql
select UNIX_TIMESTAMP() from DUAL;/*结果：1631516881*/
select FROM_UNIXTIME(1631516881,'%Y-%m-%d') from dual;/*结果:2021-09-13*/
```

> 在实际开发中，我们也经常使用int来保持一个nuix时间戳，然后使用from_unixtime()进行转换，还是非常实用价值的



## 加密函数和系统函数

（1）查询用户(也就是可以查看登录到mysql有哪些用户以及登录的IP)

```sql
select user() from DUAL;
```

（2）查询当前使用数据库名称

```sql
select database();
```

（3）为字符串算出一个MD5 32的字符串，常用（用户密码）加密，以下演示

```sql
/*crate users_test*/
create table users_test(id int,name varchar(32) not null default '',
pwd char(32) not null default '');
insert into users_test values(10,'诸葛亮',MD5('123456'));
SELECT * from users_test where name = '诸葛亮' and pwd = MD5('123456');
/*加密后存储:(e10adc3949ba59abbe56e057f20f883e)*/
```

（4）PASSWORD(str)：加密函数，mysql数据库的用户密码就是password函数加密的

```sql
SELECT PASSWORD('123456') from DUAL
```



## 单表查询(复习)

1、查询加强(复习基本单表查询)

需求：

（1）使用where子句，如何查找199.1.1后入职的员工

```sql
select * from emp where hiredate > '1990-12-15';
```

（2）如何使用like操作符

`%`：表示0到多个字符

`_`：表示单个字符

如何显示首字符为s的员工姓名和工资

```sql
select ename,sal from emp where ename like 's%';
```

如何显示第三个字符为大写的O的所有员工的姓名和工资

```sql
select ename,sal from emp where ename like '__n%';
```

（3）如何显示没有上级的员工的情况

```sql
select * from emp where mgr is null;
```

（4）查询表结构selecting.sql

```sql
desc emp;
```

2、order by

需求：

（1）如何按照工资的从低到高的顺序，显示员工信息（默认升序）

```sql
select * from emp order by sal asc;
```

（2）如何按照部门升序员工的工资降序排列，显示员工信息

```sql
select * from emp order by deptno asc,sal desc;
```

3、分页查询

公式：

```sql
select * from emp order by empno limit 每页显示记录数*(第几页-1)，每页显示记录数;
```

需求：

（1）按照员工的ID编号升序取出，每页显示3条记录，请分别显示，第一页、第二页、第三页

```sql
select * from emp order by empno limit 0,3;
```

（2）按照员工的ID编号升序取出，每页显示5条记录，请分别显示，第3页、第5页

```sql
select * from emp order by empno desc limit 15,5;
select * from emp order by empno desc limit 20,5;
```

4、分组增加

（1）显示每种岗位的雇员总数、平均工资

```sql
select count(*),avg(sal),job from emp group by job;
```

（2）显示雇员总数，以及获得补助的雇员

```sql
select count(*),count(if(comm is null,1,null)) from emp;
```

（3）显示管理者的总数

```sql
select count(distinct mgr) from emp;
```

（4）显示雇员工资的最大差额

```sql
select max(sal) -min(sal) from emp;
```

5、数据分组总结

如果select语句同时包含group by、having、limit、order by那么他们的顺序应该是这样的，顺序不能乱写的

```sql
select ...from table
                    group by ...
                    having ...
                    order by ...
                    limit start,rows;
```



## 多表查询(重点，难点)

1、多表查询在我们实际开发中，是非常重要的。多表查询是指基于两个和两个以上的表查询，在实际应用中，查询单个表可能无法满足需求，

2、多表查询案例

（1）显示雇员名，雇员工资及所在部门的名字

分析：

* 雇员名，雇员工资来自emp表
* 部门的名字来自dept表
* 需求就是对这两张表查询

```sql
select * from emp,dept;/*大家执行这条语句和下面的语句对比，有什么不同，为什么？*/
select ename,sal,dname from emp,dept where emp.deptno=dept.deptno;
```

> 在默认情况下，当两张表查询时，规则：
>
> * 从第一张表中，取出一行和第二张表的每一行进行组合，返回结果【含有两张表的所有列】
> * 一共返回的记录第一张表行数*第二张表的列数
> * 这样多查询默认处理返回的结果，称为笛卡尔集
> * 解决这个多个多表的关键就是要写出正确的过滤条件where，需要程序猿进行分析

（2）多表查询的条件不能少于表的个数-1，否则会出现笛卡尔集

（3）如何显示部门号为10的部门名，员工名和工资

```sql
select ename,sal,dname,emp.deptno from emp,dept where emp.deptno=dept.deptno and emp.deptno = 10;
```

（4）显示各个员工的姓名，工资，及其工资的级别

```sql
select * from emp;
select * from salgrade;
select ename,sal,grade from emp,salgrade where sal between losal and hisal;
```

3、多表查询--自连接

自连接是指在同一张表的连接查询【将同一张表看作两张表】self.sql

显示雇员名字在emp，上级的名字的名字emp

```sql
select worker.ename as '雇员名',boss.ename as '上级名' from emp worker,emp boss where worker.mgr=boss.empno;
/*
- from emp worker,emp boss，一张表认为是worker，另一张表认为是boss表（这就是把一张表当着两张表使用）
- where worker.mgr=boss.empno：过滤就是雇员的上级等于boss的编号
*/
```

4、mysql表—子查询

* 子查询：子查询是指嵌入在其他SQL语句中的select语句，也就叫嵌套查询。

* 单行子查询：单行子查询是指只返回一行数据的子查询语句

  * 练习：如何显示与SMITH同一部门的所有雇员

  * ```sql
    select * from emp where deptno = (
    select deptno from emp where ename = 'smith')
    ```

* 多行子查询：多行子查询指返回多行数据的子查询，使用关键字`in`

练习：如何查询和部门10的工作相同的雇员的名字、岗位、工资、部门号、但是不含10自己的？

```sql
select ename,job,sal,deptno
from emp
where job in(
select distinct job from emp where deptno = 10)
and deptno <> 10;
/*从emp查找ename,job,sal,deptno这些信息，job工作*/
```

5、all 和 any的使用

显示工资比部门30号所有员工的工资高的员工的姓名、工资和部门号

```sql
select ename,sal,deptno from emp where sal > all(select sal from emp where deptno = 30)
```

显示工资比部门30号的其中一个员工的工资的员工的姓名、工资和部门号

```sql
select ename,sal,deptno from emp where sal > any(select sal from emp where deptno = 30)
```

6、多列子查询

如何查询与smith的部门和岗位完全相同的所有雇员（且不含smith本人）

```sql
select deptno,job from emp where ename = 'smith';/*将该语句当子查询使用*/
select deptno,job from emp where ename = 'smith';/*将该语句当子查询使用*/
select * from emp where (deptno,job) = (
  select deptno,job from emp where ename = 'smith';
) and ename != 'smith';
```

7、自我复制

有时候，为了对某个SQL语句进行效率测试，我们需要海量数据时，可以使用此法为表创建海量数据

```sql
insert into my_tab01 select * from my_table01;
```

如何删除掉一张表重复记录

* 先创一张表my_tabl02
* 让my_tab02有重复的记录

```sql
create table my_tab02 like emp;/*意思是把emp表结构（列），复制到my_tab02*/
desc my_tab02;
insert into my_tab02 select * from emp;/*将emp数据也插入my_tab02*/
/*
如何删除掉一张表重复记录？
去重步骤：
（1）先创建一个临时表my_tmp，该表的结构和my_tab02一样
（2）把my_tmp的记录，通过distinct关键字，处理后把记录复制到my_tmp
（3）清除掉my_tab02记录
（4）把my_tmp表记录复制到my_tab02
（5）drop掉临时表my_tmp
*/
create table my_tmp like my_tab02;
insert into my_tmp select distinct * from my_tab02;
delete from my_tab02;
insert into my_tab02 select * from my_tmp;
drop table my_tmp;
select * from my_tab02;
```

8、合并查询

union该操作与union all相似，但是会自动去掉结果集中重复行

9、mysql表连接查询（必须掌握，在纸上手写几次就记住了）

```sql
# 内联
select * from tbl_emp a inner join tbl_dept b on a.deptld = b.id;
# 左联
select * from tbl_emp a left  join tbl_dept b on a.deptld = b.id;
# 右联
select * from tbl_emp a right  join tbl_dept b on a.deptld = b.id;
# 左独有
select * from tbl_emp a left  join tbl_dept b on a.deptld = b.id where b.id is null;
# 右独有
select * from tbl_emp a right  join tbl_dept b on a.deptld = b.id where a.deptld is null;
# 全有
select * from tbl_emp a full outer  join tbl_dept b on a.deptld = b.id;
# 合并,union具有去重功能
select * from tbl_emp a inner join tbl_dept b on a.deptld = b.id union select * from tbl_emp a left  join tbl_dept b on a.deptld = b.id;
# 左独有 右独有
select * from tbl_emp a inner join tbl_dept b on a.deptld = b.id where b.id is null  union select * from tbl_emp a left  join
tbl_dept b on a.deptld = b.id where a.deptld is null;
```

## 主键

```sql
/*创建，且设置ID为主键*/
create table t1 (id int primary key,name varchar(32),email varchar(32));
/*插入数据*/
insert into t1 values(1,'tmo','tmo@qq.com');
insert into t1 values(2,'jack','jack@qq.com');
/*下面这条语句报错：1062 - Duplicate entry '1' for key 't1.PRIMARY',*/
insert into t1 values(1,'mark','mark@qq.com');
```

* primary key不能重复而去不能为null

* 一张表最多只能有一个主键，但是可以复合主键

  * ```sql
    create table t2 (id int,
                     name varchar(32),
    								 email varchar(32),
    								 primary key (id,name))/*primary key(id,name)这里就是复合键*/
    ```

* 在实际开发中，每个表往往都有一个主键

## Unique

```sql
/*加入unique，表示ID列不能重复*/
create table tt (id int unique,name varchar(32),email varchar(32));
```

## 外键

用于定义主表和从表之间的关系：外键约束要定义在从表上的，主表则必须具有主键约束或是unique约束，当定义外键约束后，要求外键列数据必须在主表的主键列存在或者为null

```sql
/*主表*/
create table my_class(id int PRIMARY key,name varchar(32) not null default '')
/*从表*/
create table my_stu(id int PRIMARY key,name varchar(32) not null default '',
                    class_id int,foreign key(class_id) references my_class(id));
/*插入数据*/
insert into my_class values(100,'java'),(200,'python')
insert into my_stu values(1,'tom',100),(2,'jack',200)
insert into my_stu values(3,'tom',1000)/*失败的因为my_class没有1000*/
```

## check

用于强行数据必须满足的条件，假定在sal列上定义了check约束，并要求sal列值在1000-2000之间，如果不在该期间就会提示出错。

注意：oracle 和 SQL server均支持check，但是mysql5.7目前还不支持check，只做语法校验，但不生效。

```sql
create table tt23(id int PRIMARY key,name varchar(32),sex varchar(6) check(sex in('man','woman')),
sal DOUBLE check (sal > 10000 and sal < 20000));
/*以下插入数据会失败，190000不在范围内*/
insert into tt23 values(2,'jack','woman',190000);
```

## 练习

```sql
/*商品表*/
create table goods(
googs_id int primary key,
goods_name varchar(64) not null default '',
unitprice decimal(10,2) not null default 0
check(unitprice >=1.0 and unitprice<=999.99),
category int null default 0,
privider varchar(64) not null default ''
);
/*各户表*/
create table customer(
customer_id int(64) primary key,
name varchar(64) not null default '',
address varchar(64) not null default '',
email varchar(64) unique not null,
sex enum('男','女') not null,
card_id char(18)
);
/*订单表*/
create table purchase(
order_id int primary key,
customer_id int(64) not null default '',
googs_id int not null default 0,
nums int not null default 0,
foreign key (customer_id) references customer(customer_id),
foreign key (goods_id) references goods(googs_id)
);
```

## 自增长

```sql
create table tt25(
id int primary key AUTO_increment,
email varchar(32) not null default '',
’name‘ varchar(32) not null default ''
)
insert into tt25 values(null,'aa@qq.com','tom')
```

## mysql索引

1、索引是用来提高数据库性能的，索引是最物美价兼的东西，不用加内存，不能改程序，不用调优，查询速度能提高几百倍。

创建索引：

* empno_index：索引名称
* on emp(empno)：表示在emp表的empno列创建索引

```sql
create index empno_index on emp(empno);
```

2、索引原理

没有索引为什么会慢？

在没有添加索引时，查询是全表扫面的，所以速度非常的慢。

使用索引为什么会快？

使用索引之后，它会进行B+树查找，速度提高了不少

索引的代价？

磁盘开销以及删除、插入、更新有一定影响

3、索引类型

主键索引：主键自动的为主键索引（类型primary key）

唯一索引：（unique）

普通索引：（index）

全文索引：（fulltext）适用于myISAM

注：开发中一般不使用mysql自带的全文索引，常用的全文索引solr 和 ElasticSearch

4、索引创建、删除

```sql
/*先创建表*/create table t25 (id int,‘name’ varchar(32));/*添加唯一索引*/create unique index id1_index on t25(id);/*添加普通索引*/create index id_index on t25(id);/*添加主键索引*/ALTER table t25 add primary key(id);/*删除索引*/drop index id_indenx on t25;/*删除主键索引*/alter table t25 frop primary key;/*查询索引3种方式*/show indexes from t25;show index from t25;show keys from t25;
```

如何选择索引？如果某列的值，是不会重复的，则优先考虑使用unique索引；否则使用普通索引。

总结：

* 较频繁的作为查询条件字段应该创建索引
* 唯一性差的字段不适合单独创建索引，即使频繁作为查询
* 更新非常频繁的字段不合适创建索引
* 不会出现在where子句中字段不该创建索引



## mysql事务

1、什么是事务？

事务用于保证数据的一致性，它由一组相关的dml语句组成，该组的dml语句要么全部成功，要么全部失败。

事务锁

* 当执行事务操作时，mysql会在表上加锁，防止其它用户更改表的数据

模拟事务：

```sql
create table t26(id int,name varchar(32))# 1、开启一个事务start transaction;# 2、设置保存点savepoint a;insert into t26 values(100,'tom');select * from t26;# 设置保存点savepoint b;insert into t26 values(200,'jack');# 3、回退事务rollback;#回退到最开始的事物rollback to b;# 4、提交事务，所生成的操作生效，不能回退commit
```



## mysql 事务隔离级别

1、多个连接开启各自事务操作数据库中的数据时，数据库系统要负责隔离操作，以保证各个连接在获取数据时的准确性。

如果不考虑隔离性，可能出现哪些问题？

（1）脏读（dirty read）

当一个事务读取另一个事务尚未提交修改时，产生脏读。

（2）不可重复读（nonrepeatable read）

同一查询在同一事务中多次进行，由于其它提交事务所做的修改或删除，每次返回不同的结果集，此时发生不可重复读。

（3）幻读（phantom read）

同一查询在同一事务中多次进行，由于其它提交事务，所以做的插入操作，每次返回不同的结果集，此时发生幻读。

2、事务隔离级别

> 概念：mysql隔离级别定义了事务与事务之间的隔离程度。

4中隔离级别如下表：

| 4种隔离级别                | 脏读 | 不可重复读 | 幻读 |  加锁  |
| :------------------------- | :--: | :--------: | :--: | :----: |
| 读未提交(Read uncommitted) |  √   |     √      |  √   | 不加锁 |
| 读已提交(Read committed)   |  x   |     √      |      | 不加锁 |
| 可重复读(Repeatable read)  |  x   |     x      |  x   | 不加锁 |
| 可串行化(Serializable)     |  x   |     x      |  x   |  加锁  |

3、设置隔离

（1）查看当前会话隔离级别

```sql
select @@tx_isolation
```

（2）查看系统当前隔离级别

```sql
select @@global.tx_isolation
```

（3）设置当前会话隔离级别

```sql
et session transaction isolation level read uncommitted
```

（4）设置系统当前隔离级别

```sql
set session transaction isolation level [read uncommitted]设置需求的级别
```

（5）mysql默认的事务隔离级别时repeatable read，一般情况下，灭蝇特殊要求，灭蝇必要修改（因为该级别可以满足大部分项目需求）

4、mysql事务ACID的特性

* 原子性(Atomicity)：原子性是指事务是一个不可分割的工作单位，事务中的操作要么成功，要么失败
* 一致性(Consistency)：事务必须使数据库从一个一致性状态变换到另一个一致性状态
* 隔离性(Isolation)：事务的隔离性是多个用户并发访问数据库时，数据库为每个用户开始事务，不能被其他事务的操作数据所干扰，多个并发事务之间相互隔离。
* 持久性(Durability)：持久性是一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据发生故障也不应该对其有任何影响。



## mysql 存储引擎

（1）基本介绍

* mysql的表类型由存储引擎(storage Engines)决定，主要包括myISAM、InnoDB、memory等。（查看所有存储引擎：`show engines`）
* mysql数据表主要支持六种类型，分别是：CSV、memory、ARCHIVE、MRG_MYISAM、MYISAM、InnoBDB
* 这六种又分为两类：一类是“事务安全型(transaction-safe)”比如：InnoDB；其余都属于第二类称为“非事务安全型(non-transaction-safe)”

（2）myISAM、InnoDB、MEMORY

* MyISAM不支持事务、也不支持外键、其他访问速度快，对事务完整性没有要求。

  * ```sql
    # 设置myisam
    create table t28(id int,name varcahr(32) engine myisam)
    ```

* InnoDB存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全。但是比起MyISAM存储引擎，InnoDB写的处理效率差一些，并且会占用更多的磁盘空间以及保留数据和索引。（支持事务、支持外键、支持行级锁）

* MEMORY存储引擎存在内存中的内容来创建表。每个MEMORY表只实际对应并且默认使用Hash索引，但是一旦服务关闭，表中的数据就会丢失，表的结构还在。执行速度快（没有IO读写）

  * ```sql
    create table t28(id int,name varcahr(32) engine memory)
    ```

### 如何选择表的存储引擎？

* 如果你的引用不需要事务，处理的只是基本的CRUD操作，那么myISAM是不二选一速度快。
* 如果需要支持事务，选择InnoDB
* memory存储引擎就是将数据存储咋内存中，由于没有磁盘I/O的等待，速度极快。但是由于内存引擎，所做的任何修改在服务器重启后都将消失



更多文章已被Github收录：https://github.com/niutongg/JavaLeague
