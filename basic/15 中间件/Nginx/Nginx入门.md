Nginx介绍

它是一个高性能的HTTP和反向代理服务器，并发能力强。

反向代理：

负载均衡

动静分离

常用命令：

```
启动
sudo systemctl start nginx  
停止  
sudo systemctl stop nginx
# 重加载
nginx -s reload
```

#### 配置文件路径：/etc/nginx(yum安装默认路径)

三部分组成：

（1）全局块

lai主要设置一些影响nginx服务器整体运行的配置指令包括：运行Nginx服务器的用户组、运行生成的work process数、进行PID存放路径、日志存放路径和类型及配置的引入等。

work process 1; # 值越大，可以支持的并发处理量越多，但是会受到硬件、软件的制约。

（2）events

events 块主要配置nginx服务器和用户的网络连接

比如：worker_connections 1024;# 支持的最大连接数

（3）http块（包括http全局块和server块）配置最频繁的（**重点掌握**）

* Http全局块：包括文件引入、MIME-TYPE定义、日志自定义、超链接时间、但连接请求数上线等
* server块：和虚拟主机有密切关系，虚拟主机从用户角度看，和独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件的成本。

```nginx
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;
events {
    worker_connections 1024;
}
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
    server {
        listen       80; # 表示目前Nginx监听端口号是80端口
        listen       [::]:80;
        server_name  _; # 主机名称
        root         /usr/share/nginx/html;
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        error_page 404 /404.html;
        location = /404.html {
        }
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2;
#        listen       [::]:443 ssl http2;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers HIGH:!aNULL:!MD5;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }
}
```

```
启动
sudo systemctl start nginx  
停止  
sudo systemctl stop nginx
启动：  systemctl start firewalld
查状态：systemctl status firewalld 
停止：  systemctl disable firewalld
禁用：  systemctl stop firewalld
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed
```



反向代理

nginx配置实例1-反向代理:

> 1、实现效果：
>
> （1）打开浏览器，在浏览器地址栏输入地址www.xxx.com，然后会跳转Linux系统的tomcat主页面
>
> （2）准备工作
>
> * 在Linux安装tomcat，使用默认端口
> * 对外开发访问端口号
>   * Firewall-cmd --add-port=8080/tcp --permanent
>   * Firewall-cmd --reload
> * 查看已经开放的端口号：firewall-cmd --list-all
>
> （3）测试：http://你的IP地址:8080/
>
> 2、具体步骤
>
> （1）本地hosts配置主机映射：192.168.5.2  www.123.cm（然后浏览器输出即可访问）
>
> （2）进入nginx配置文件
>
> ```
> server {
>         listen       80; 
>         listen       [::]:80;
>         # server_name  _; 
>         server_name  192.168.0.160; #配置IP
>         root         /usr/share/nginx/html;
>         # Load configuration files for the default server block.
>         include /etc/nginx/default.d/*.conf;
>         error_page 404 /404.html;
>         location = /404.html {
>         proxy_pass http://127.0.0.1:8080; #第二处反向 
>         }
>         error_page 500 502 503 504 /50x.html;
>         location = /50x.html {
>         }
>     }
> ```
>
> 

nginx配置实例2-反向代理:

> 1、实现效果：使用nginx反向代理，根据访问路径跳转不同端口的服务器，nginx监听端口为9001
>
> 访问http://127.0.0.1:9001/edu/   直接跳转127.0.0.1:8080
>
> 问http://127.0.0.1:9001/vod/       直接跳转127.0.0.1:8082
>
> 2、准备工作
>
> （1）两个tomcat服务器，8080和8081
>
> （2）创建文件测试
>
> 3、具体配置
>
> （1）找到Nginx配置文件，进行反向代理的
>
> ```
>         listen         9001;
>         server_name 192.168.0.160;
>         location ~ /edu/{
>             proxy_pass http://127.0.0.1:8080;
>         }
>         location ~ /vod/ {
>              proxy_pass http://127.0.0.1:8081;
>         }
> ```
>
> （2）开放端口







