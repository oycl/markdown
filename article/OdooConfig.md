# Odoo项目配置

## 一，conf文件配置
### 准备工作
创建data目录
```sh
(venv) odoo@$ cd /home/odoo/odoo10
(venv) odoo@$ mkdir data
```
创建日志目录
```sh
# mkdir /var/log/odoo
# chown -R odoo:odoo /var/log/odoo
```
增加workers
```sh
(venv) odoo@$ python
>>>import multiprocessing
>>>multiprocessing.cpu_count()
>>>2
```
>  获得cpu个数+1 为workers个数，更多请看本站 odoo性能调优介绍

建立模块目录
```sh
(venv) odoo@$ mkdir /home/odoo/odoo10/custom-addons
```

### 编写配置文件
```sh
(venv) odoo@$ vim /home/odoo/odoo10/odoo10.conf
```

### 在conf文件中输入
```sh
[options]
db_template = template0
db_host = 127.0.0.1
db_port = 5432
db_name = odoo10
db_user = odoo
db_password = odoodb***1
admin_passwd = odooweb***1
xmlrpc_port = 8069
data_dir = /home/odoo/odoo10/data
log_level = warn
logfile = /var/log/odoo/odoo10.log
workers = 3
addons_path = /home/odoo/odoo10/custom-addons,/home/odoo/odoo10/addons
```
内容详解
1. admin_passwd 启用web数据库管理密码 admin_passwd（不能设置为admin，无效密码）
2. xmlrpc_interface 监听IP段改为本地（127.0.0.1）, 可实现禁止外网直接访问odoo（默认0.0.0.0）
   外网包括局域网，所以只能去掉xmlrpc_interface = 127.0.0.1
3. xmlrpc_port 监听端口（默认8069）
4. 注意db_user 和db_password 和之前设置的数据库里的用户名一致
5. log_level = info
6. 如果odoo是以nightly包的形式安装的，则核心模块和业务模块都放在一个目录里
/home/odoo/odoo10/venv/lib/python2.7/site-packages/odoo-10.0.post20170408-py2.7.egg/odoo/addons
可以不配置addons_path参数

### 手动启动服务器测试
```sh
# su odoo
$ source venv/bin/activate
(venv) odoo@$ chmod 755 odoo-bin
(venv) odoo@$ ./odoo-bin -c odoo10.conf
```

浏览器访问 http://Server_IP:8069

## 二，配置开机启动脚本

使用systemd模式，如不了解请看FAQ。
```sh
# vim /etc/systemd/system/odoo10.service
```
配置内容
```sh
[Unit]
Description=Odoo10
Requires=postgresql.service
After=postgresql.service
[Service]
Type=simple
PermissionsStartOnly=true
User=odoo
Group=odoo
SyslogIdentifier=odoo10
ExecStart=/home/odoo/odoo10/venv/bin/python /home/odoo/odoo10/odoo-bin -c /home/odoo/odoo10/odoo10.conf
[Install]
WantedBy=default.target
```
运行
```sh
# systemctl enable odoo10.service
# systemctl start odoo10.service
```
这样配置之后，ubuntu重启了，odoo服务自动启动（nginx和postgresql的自启动服务系统已经帮我们添加好了）

使用systemctl status odoo10.service 可以了解当前服务的状态。
```sh
# ps -ef
# systemctl status odoo10.service
```
停止服务
```sh
# systemctl stop odoo10.service
```

之后更新odoo10.service,需通过systemctl daemon-reload和systemctl restart odoo10.service重启odoo进程，
```sh
# systemctl restart odoo10.service
```

## 三，配置nginx反向代理
安装，新建odoo10_nginx.conf
```sh
# apt-get install nginx 
# rm /etc/nginx/sites-enabled/default
# vim /etc/nginx/conf.d/odoo10_nginx.conf
```
添加内容
```sh
upstream odoo {
    server 127.0.0.1:8069;
}
upstream odoochat {
    server 127.0.0.1:8072;
}

server {
    listen 80;

    # proxy
    proxy_buffers 16 64k;
    proxy_buffer_size 128k;

    proxy_connect_timeout 600s;
    proxy_send_timeout 600s;
    proxy_read_timeout 600s;
    proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;

    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    #gzip
    gzip_types text/css text/less text/plain text/xml application/xml application/json application/javascript;
    gzip on;

    location / {
        access_log /var/log/nginx/odoo10_access.log;
        error_log /var/log/nginx/odoo10_error.log;
        proxy_redirect off;
        proxy_pass http://odoo;
        client_max_body_size 500m;
    }

    location /longpolling {
        proxy_redirect off;
        proxy_pass http://odoochat;
    }
}
```
重启nginx服务，生效
```sh
# service nginx restart
```
> 注意如过设置了longpolling单独转发upstream，下文中设置workers不能为0（打印支持必须大于1）

浏览器访问 http://Server_IP

### 使用nginx日志里面会有报错
后来认为于精简模块无关，内网环境下继续观察这个问题

2017-11-14 05:54:36,216 877 WARNING admin odoo.http: No CSRF validation token provided for path '/'

Odoo URLs are CSRF-protected by default (when accessed with unsafe
HTTP methods). See
https://www.odoo.com/documentation/9.0/reference/http.html#csrf for
more details.

* if this endpoint is accessed through Odoo via py-QWeb form, embed a CSRF
  token in the form, Tokens are available via `request.csrf_token()`
  can be provided through a hidden input and must be POST-ed named
  `csrf_token` e.g. in your form add:

      <input type="hidden" name="csrf_token" t-att-value="request.csrf_token()"/>

* if the form is generated or posted in javascript, the token value is
  available as `csrf_token` on `web.core` and as the `csrf_token`
  value in the default js-qweb execution context

* if the form is accessed by an external third party (e.g. REST API
  endpoint, payment gateway callback) you will need to disable CSRF
  protection (and implement your own protection if necessary) by
  passing the `csrf=False` parameter to the `route` decorator.

目前还没有是用nginx很好的解决方法，有时间参考
[维基百科](https://en.wikipedia.org/wiki/Cross-site_request_forgery)

### 重定向端口
打开文件
vim /etc/rc.local
```sh
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8069
```
然后重启，观察效果


## 四，配置postgresql
### 允许远程连接
#### 问题一
如果报错 
```sh
could not connect to server: Connection refused (0x0000274D/10061) Is the server 
running on host "120.92.82.112" and accepting TCP/IP connections on port 5432?
```
编辑配置文件
```sh
vim /etc/postgresql/9.5/main/postgresql.conf
```
找到“#listen_addresses = 'localhost'”，把它改成“listen_addresses = '*'”。
或者修改listen_addresses为对外的interface的ip地址，如'172.17.235.26'一样可以 
但是如果改成'172.17.235.26'则odoo连接该IP会有问题，干脆改成'\*'。这样，postgresql就可以监听所有ip地址的连接。 

重新启动数据库
```sh
# service postgresql restart
```
或者
```sh
systemctl status postgresql.service
```

#### 问题二
PostgreSQ数据库为了安全，它不会监听除本地以外的所有连接请求，
当用户通过JDBC访问是，会报一些如下的异常：
```sh
org.postgresql.util.PSQLException: FATAL: no pg_hba.conf entry for host
```
pgAdmin登录时也会报错：
```sh
FATAL: no pg_hba.conf entry for host "218.10.12.207", user "postgres", database "testdb", 
SSL off FATAL: no pg_hba.conf entry for host "218.10.12.207", user "postgres", database "testdb", SSL on
```
要解决这个问题，windows下只需要在PostgreSQL数据库的安装目录下找到/data/pg96/pg_hba.conf
linux下
```sh
vim /etc/postgresql/9.5/main/pg_hba.conf
```
找到
```sh
# IPv4 local connections:
host all all 127.0.0.1/32 md5
```
在其下加上一行请求连接的机器IP
host all all 218.10.12.207/32 md5
32是子网掩码的网段，用32表示该IP被固定，用24表示前3位固定，后面一位可以由自己设；md5是密码验证方法，
例：如果为“host all all 192.168.91.1/24 md5” ，则前3位ip地址与该设定相同的计算机就可以访问postgresql数据库。

重新启动数据库

### 运行PgSQL函数

### 数据库执行计划任务




[back](../)