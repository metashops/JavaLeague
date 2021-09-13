### SQL语句分类

* DDL：数据定义语句
* DML：数据操作语句【增删改查】
* DQL：数据查询语句【select】
* DCL：数据控制语句【管理数据库】



### MYsql列类型

数据型（整形）的基本使用：tinyint[-128-127]、smallint[-32768-32767] 、 int

* 文本类型：Char(0-255) 、 varcher(0-65535)[0~2^16-1] 、 text(0~2^16-1) 、 longtext(0~2^32-1)

* 二进制数据类型：blob[0~2^16-1] 、 long blob[0~2^32-1]

* 日期类型：date[日期:年月日] 、time[时间:时分秒] 、 datetime[年月日时分秒YYYY-MM-DD HH:mm:ss]、timestamp[时间戳]

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



### 统计函数

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



### 分组统计

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



### 日期函数

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



### 加密函数和系统函数

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

