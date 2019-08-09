# Postgresql数据库安装


## 1，依赖安装

```sh
# apt-get install libpq-dev -y
```

## 2，安装postgresql数据库
### 安装
> ubuntu16.04的postgresql是9.5的, 建议开发和部署使用相同版本。
> 需要安装不同版本的postgresql可以参考https://devecho.com/v/postgresql-basic/

```sh
# apt-get install postgresql-9.5 postgresql-server-dev-9.5
```

### 设置
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



[back](./)
