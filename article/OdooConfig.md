# Odoo项目配置

## 一，conf文件配置
### 编写配置文件
```sh
(venv) odoo@$ vim /home/odoo/odoo10/odoo10.conf
```
最简单的配置内容
```sh
[options]
db_template = template1
db_host = 127.0.0.1
db_port = 5432
db_name = odoo10
db_user = odoo
db_password = odoodb***1
admin_passwd = odooweb***1
xmlrpc_interface = 127.0.0.1
xmlrpc_port = 8069
```
内容详解
1. 启用web数据库管理密码 admin_passwd（不能设置为admin，无效密码）
2. 监听IP段改为本地（127.0.0.1）, 可实现禁止外网直接访问odoo（默认0.0.0.0）
3. 监听端口（默认8069）
4. 注意db_user 和db_password 和之前设置的数据库里的用户名一致



### 创建并配置data目录
创建data目录
```sh
(venv) odoo@$ cd /home/odoo/odoo10
(venv) odoo@$ mkdir data
```
在odoo10.conf里面加上
```sh
data_dir = /home/odoo/odoo10/data
```

### 配置日志选项
```sh
# mkdir /var/log/odoo
# chown -R odoo /var/log/odoo
# su odoo
$ source venv/bin/activate
```

在odoo10.conf里面加上
```sh
log_level = warn
logfile = /var/log/odoo/odoo10.log
```
log_level = info

### 增加workers
使用下面命令
```sh
(venv) odoo@$ python
>>>import multiprocessing
>>>multiprocessing.cpu_count()
>>>2
```
获得cpu个数+1 为workers个数，在odoo10.conf里面加上
```sh
workers = 3
```
更多请看本站 odoo性能调优介绍

### 添加自己的模块目录
建立目录
```sh
mkdir /home/odoo/odoo10/custom-addons
```
在odoo10.conf里面加上
```sh
addons_path = /home/odoo/odoo10/custom-addons,/home/odoo/odoo10/addons
```

> 如果odoo是以nightly包的形式安装的，则核心模块和业务模块都放在一个目录里
/home/odoo/odoo10/venv/lib/python2.7/site-packages/odoo-10.0.post20170408-py2.7.egg/odoo/addons
> 可以不配置addons_path参数

### 把E版文件拷贝进custom-addons（视应用环境而定）
### 测试删除/home/odoo/odoo10/addons里面模块，把自己的模块拷贝进custom-addons


### 启动服务器测试
```sh
(venv) odoo@$ chmod 755 odoo-bin
(venv) odoo@$ ./odoo-bin -c odoo10.conf
```

浏览器访问 http://Server_IP:8069




## 二，配置开机启动脚本

重启odoo服务
```sh
# systemctl restart odoo10.service
```



## 三，配置nginx反向代理



## 四，配置postgresql
### 允许远程连接

### 运行PgSQL函数

### 数据库执行计划任务




### And an ordered list:
1.  Item one
1.  Item two
1.  Item three
1.  Item four

### Here is an unordered list:
*   Item foo
*   Item bar
*   Item baz
*   Item zip

> 注释
> **强调注释**

内容

```sh
shell get 'var'
```

```yml
show_downloads: ["true" or "false" to indicate whether to provide a download URL]
google_analytics: [Your Google Analytics tracking ID]
```

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

内嵌代码`su root`

[链接](http://123.com/art/abc.htm)

### Small image

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![](https://guides.github.com/activities/hello-world/branching.png)

[back](../)