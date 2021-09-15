### 主键

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

### Unique

```sql
/*加入unique，表示ID列不能重复*/
create table tt (id int unique,name varchar(32),email varchar(32));
```

### 外键

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

### check

用于强行数据必须满足的条件，假定在sal列上定义了check约束，并要求sal列值在1000-2000之间，如果不在该期间就会提示出错。

注意：oracle 和 SQL server均支持check，但是mysql5.7目前还不支持check，只做语法校验，但不生效。

```sql
create table tt23(id int PRIMARY key,name varchar(32),sex varchar(6) check(sex in('man','woman')),
sal DOUBLE check (sal > 10000 and sal < 20000));
/*以下插入数据会失败，190000不在范围内*/
insert into tt23 values(2,'jack','woman',190000);
```

### 练习

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

### 自增长

```sql
create table tt25(
id int primary key AUTO_increment,
email varchar(32) not null default '',
’name‘ varchar(32) not null default ''
)
insert into tt25 values(null,'aa@qq.com','tom')
```

### mysql索引

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
/*先创建表*/
create table t25 (id int,‘name’ varchar(32));
/*添加唯一索引*/
create unique index id1_index on t25(id);
/*添加普通索引*/
create index id_index on t25(id);
/*添加主键索引*/
ALTER table t25 add primary key(id);
/*删除索引*/
drop index id_indenx on t25;
/*删除主键索引*/
alter table t25 frop primary key;
/*查询索引3种方式*/
show indexes from t25;
show index from t25;
show keys from t25;
```

如何选择索引？如果某列的值，是不会重复的，则优先考虑使用unique索引；否则使用普通索引。

总结：

* 较频繁的作为查询条件字段应该创建索引
* 唯一性差的字段不适合单独创建索引，即使频繁作为查询
* 更新非常频繁的字段不合适创建索引
* 不会出现在where子句中字段不该创建索引

### mysql事务

1、什么是事务？

事务用于保证数据的一致性，它由一组相关的dml语句组成，该组的dml语句要么全部成功，要么全部失败。

事务锁

* 当执行事务操作时，mysql会在表上加锁，防止其它用户更改表的数据

模拟事务：

```sql
create table t26(id int,name varchar(32))
# 1、开启一个事务
start transaction;
# 2、设置保存点
savepoint a;
insert into t26 values(100,'tom');
select * from t26;
# 设置保存点
savepoint b;
insert into t26 values(200,'jack');
# 3、回退事务
rollback;#回退到最开始的事物
rollback to b;
# 4、提交事务，所生成的操作生效，不能回退
commit
```



### mysql 事务隔离级别

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