1、官网下载

2、然后安装下一步，知道设置密码然后安装结束

3、测试安装是否成功

```
终端输入：mysql
出现：zsh: command not found: mysql
```

说明没有配置环境，通过vim编辑 .zshrc 配置文件

```
export PATH=$PATH:/usr/local/mysql/bin
```

然后在执行 source ~/.zshrc 使环境变量配置生效，这样配置成功

4、进行mysql

```
mysql -u root -p
```

5、查看编码格式

```
show variables like '%char%';
```

![Screen Shot 2021-08-01 at 22.19.47.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gt1nky25n3j60sg0egwkj02.jpg)

看到其中显示`utf8mb4`，在最新版的mysql中，是默认安装这个的。它和`utf8`完全兼容，并且还支持Unicode标准.

```
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
rpm -ivh mysql80-community-release-el7-3.noarch.rpm
# 卸载之前版本
rpm -qa | grep mariadb
# 安装
yum install  mysql-server
# 启动
systemctl start mysqld.service #启动 mysql
systemctl restart mysqld.service #重启 mysql
systemctl stop mysqld.service #停止 mysql
systemctl enable mysqld.service #设置 mysql 开机启动
systemctl start mysqld
systemctl status mysqld
# 首次登陆密码
grep 'temporary passeord' /var/log/mysql.log
# 登陆
mysql -uroot -p 
# 远程
create user 'root'@'%' identified by '123456';
# 授权
grant all on *.* to 'root'@'%';
alter user 'root'@'%' identified with mysql_native_password by '123456';
# 5、查看编码格式
show variables like '%char%';
# 常用文件
/etc/my.cnf 　　#这是mysql的主配置文件
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```





