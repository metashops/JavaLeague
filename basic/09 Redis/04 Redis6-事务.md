#### Redis 事务常用命令

| 序号 |        命令        | 描述                                                         |
| :--: | :----------------: | ------------------------------------------------------------ |
|  1   |      DISCARD       | 取消事务，放弃执行事务块内的所有命令                         |
|  2   |        EXEC        | 执行所有事务块内的命令                                       |
|  3   |       MULTI        | 标记一个事务块的开始                                         |
|  4   |      UNWATCH       | 取消watch命令对所有key的监视                                 |
|  5   | WATCH key [key...] | 监视一个(或多个)key，如果在事务执行之前这个(或这些)key，被其他命令所改动，那么事务被打断 |

#### 事务介绍：

* Redis 的事务是通过multi、Exec、Discard 和 watch 这四个命令来完成的
* Redis 的单个命令都是原子性的，所以这里确保事务性的对象是集合命令
* 

watch

相关概念：

* 悲观锁：顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。比如，传统的关系型数据库使用到这种锁机制，如：行锁、表锁等，读锁、写锁等，都是在做操作之前先上锁的。
* 乐观锁：顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，使用版本号等机制。



#### 总结：

（1）开启：以MULTI开始一个事务

（2）入队：将多个 命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的事务队列里面

（3）执行：由EXEC命令触发事务


