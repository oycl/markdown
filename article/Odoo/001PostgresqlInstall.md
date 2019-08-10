# Postgresql数据库安装和配置

## 1，依赖安装

```sh
# apt-get install libpq-dev -y
```

## 2，安装

> ubuntu16.04的postgresql是9.5的, 建议开发和部署使用相同版本。
> 需要安装不同版本的postgresql可以参考https://devecho.com/v/postgresql-basic/

```sh
# apt-get install postgresql-9.5 postgresql-server-dev-9.5
```


## 3，配置

### 用户名密码设置

> 安装完postgresql数据库后，数据库会自动创建postgres系统用户，密码为随机，设为pg***1，home目录为/var/lib/postgresql
> 在数据库内也会创建postgres数据库用户，可以更改这个用户的密码，然后创建数据库用户odoo并设置密码

```sh
# passwd postgres
输入密码pg***1
# su postgres 
$ psql
//修改密码
postgres=# ALTER USER postgres WITH PASSWORD 'pgdb***1';
//建立用户
postgres=# create user "odoo" with password 'odoodb***1' createdb;
postgres=# \q
$ exit
```

这里有一篇详细介绍postgresql安装的文章[点击查看](https://www.cnblogs.com/zhangpengshou/p/5464610.html)


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




[back](./)
