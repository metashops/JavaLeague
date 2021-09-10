一、简介

MYSQL是一个关系型数据库

启动mysql服务后输出以下命令链接到mysql服务器

```mysql
mysql -uroot -p
Enter password:
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

开启和关闭MYSWL

```mysql
service mysql start #开启
service mysql stop #关闭
```

Mysql 查看安装位置

```mysql
ps -ef | grep mysql
```

显示数据库

```
show databases;
```

创建数据库

```
create database bd01;
```

创建完数据库然后使用

```
use bd01;
```

查看表

```
show tables;
```

创建一张表

```
create table user(id int not null,name varchar(20));
```

插入数据

```mysql
insert into user values(1,'Z3');
```

查询所有语句

```
select * from user;
```

> 如何出现乱码，那么查看编码格式，然后修改

查看编码格式和数据存储路径

```
show variables like '%char%';
```

