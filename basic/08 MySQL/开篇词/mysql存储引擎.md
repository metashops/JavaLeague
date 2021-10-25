### 1、数据库三大范式

第一范式（1NF）：强调的是列的原子性，即列不能够再分成其他几列。（数据库表中的所有字段都是单一属性，不可再分的）

第二范式（2NF）：首先是 1NF，另外包含两部分内容，一是表必须有一个主键；二是没有包含在主键中的列必须完全依赖于主键，而不能只依赖于主键的一部分。

> * 考虑一个订单明细表：【OrderDetail】（OrderID，ProductID，UnitPrice，Discount，Quantity，ProductName）。因为我们知道在一个订单中可以订购多种产品，所以单单一个 OrderID 是不足以成为主键的，主键应该是（OrderID，ProductID）。
>
> * 显而易见 Discount（折扣），Quantity（数量）完全依赖（取决）于主键（OderID，ProductID），而 UnitPrice，ProductName 只依赖于 ProductID。所以 OrderDetail 表不符合 2NF。不符合 2NF 的设计容易产生冗余数据。
>
> * 可以把【OrderDetail】表拆分为【OrderDetail】（OrderID，ProductID，Discount，Quantity）和【Product】（ProductID，UnitPrice，ProductName）来消除原订单表中UnitPrice，ProductName多次重复的情况。

第三范式（3NF）：首先是 2NF，非主键列只依赖于主键，不能依赖于其他非主键不能存在传递依赖。如：非主键列 A 依赖于非主键列 B，非主键列 B 依赖于主键的情况。

>* 考虑一个订单表【Order】（OrderID，OrderDate，CustomerID，CustomerName，CustomerAddr，CustomerCity）主键是（OrderID）。
>
>其中 OrderDate，CustomerID，CustomerName，CustomerAddr，CustomerCity 等非主键列都完全依赖于主键（OrderID），所以符合 2NF。
>
>* 不过问题是 CustomerName，CustomerAddr，CustomerCity 直接依赖的是 CustomerID（非主键列），而不是直接依赖于主键，它是通过传递才依赖于主键，所以不符合 3NF。
>
>* 通过拆分【Order】为【Order】（OrderID，OrderDate，CustomerID）和【Customer】（CustomerID，CustomerName，CustomerAddr，CustomerCity）从而达到 3NF。

在设计数据库结构的时候，要尽量遵守三范式，如果不遵守会什么样？

* 性能问题
* 数据冗余



### 2、引擎了解吗？

**常用的存储引擎三种**

* Innodb引擎：Innodb引擎提供了对数据库ACID事务的支持。并且还提供了行级锁和外键的约束。它的设计的目标就是处理大数据容量的数据库系统。InnoDB 通过使用多版本并发控制(MVCC) 来支持高并发，并且实现了SQL标准的4种隔离级别(未提交读、提交读、可重复读、可串行化)。其默认级别时可重复读（repeatable read），在可重复读级别下，通过 MVCC + Next-Key Locking 防止幻读的产生。

* MyIASM引擎(原本Mysql的默认引擎)：不提供事务的支持，也不支持行级锁和外键。

* MEMORY引擎：所有的数据都在内存中，数据的处理速度快，但是安全性不高。

**MyISAM与InnoDB区别**

* Innodb：支持外键、事务；支持哈希索引但不支持全文索引；行级锁定、表级锁定，锁定力度小并发能力高
* MyISAM：不支持外键及事务；支持全文索引，但不支持哈希索引；支持表级锁



### 3、索引有了解吗？

> 什么是索引？

索引是一种数据结构，对数据库表的一列或者多列的值进行排序一种结构，使用索引可以快速访问数据表中的特定信息。

索引有哪些优缺点？

优点：可以大大加快数据的检索速度，这也是创建索引的最主要的原因。通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

缺点：创建索引和维护索引要耗费时间，具体地，当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，会降低增/改/删的执行效率；索引需要占物理空间。

> 索引有哪几种类型？

* 主键索引: 数据列不允许重复，不允许为NULL，一个表只能有一个主键。

* 唯一索引: 数据列不允许重复，允许为NULL值，一个表允许多个列创建唯一索引。

  * 可以通过 ALTER TABLE table_name ADD UNIQUE (column); 创建唯一索引

  * 可以通过 ALTER TABLE table_name ADD UNIQUE (column1,column2); 创建唯一组合索引

* 普通索引: 基本的索引类型，没有唯一性的限制，允许为NULL值。

  * 可以通过ALTER TABLE table_name ADD INDEX index_name (column);创建普通索引

  * 可以通过ALTER TABLE table_name ADD INDEX index_name(column1, column2, column3);创建组合索引

* 全文索引： 是目前搜索引擎使用的一种关键技术。

可以通过ALTER TABLE table_name ADD FULLTEXT (column);创建全文索引

> 索引的数据结构

MySQL中使用较多的索引有**Hash索引**、**B+树索引**，通常使用的InnoDB存储引擎的默认索引实现为：B+树索引。对于哈希索引来说，底层的数据结构就是哈希表，因此在绝大多数需求为单条记录查询的时候，可以选择哈希索引，查询性能最快；其余大部分场景，建议选择BTree索引。

B Tree 指的是 Balance Tree，也就是平衡树，平衡树是一颗查找树，并且所有叶子节点位于同一层。

> B树和B+树的区别？

- 在B树中，你可以将键和值存放在内部节点和叶子节点；但在B+树中，内部节点都是键，没有值，叶子节点同时存放键和值。
- B+树的叶子节点有一条链相连，而B树的叶子节点各自独立。



### 4、事务

> 什么是数据库事务？

事务是一个不可分割的数据库操作序列，比如转账，要转成功要么失败。



> 事物的四大特性(ACID)介绍一下?

* 原子性： 事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
* 一致性： 执行事务前后，数据保持一致，多个事务对同一个数据读取的结果是相同的；
* 隔离性： 并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；
* 持久性： 一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。



> 什么是脏读？幻读？不可重复读？

* 脏读(Drity Read)：某个事务已更新一份数据，另一个事务在此时读取了同一份数据，由于某些原因，前一个RollBack了操作，则后一个事务所读取的数据就会是不正确的。
* 不可重复读(Non-repeatable read):在一个事务的两次查询之中数据不一致，这可能是两次查询过程中间插入了一个事务更新的原有的数据。
* 幻读(Phantom Read):在一个事务的两次查询中数据笔数不一致，例如有一个事务查询了几列(Row)数据，而另一个事务却在此时插入了新的几列数据，先前的事务在接下来的查询中，就会发现有几列数据是它先前所没有的。



> 什么是事务的隔离级别?

* READ-UNCOMMITTED(读取未提交)： 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
* READ-COMMITTED(读取已提交)： 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。
* REPEATABLE-READ(可重复读)： 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
* SERIALIZABLE(可串行化)： 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

**Mysql 默认采用的 REPEATABLE_READ隔离级别**
