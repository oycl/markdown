# Odoo项目配置

## 一，conf文件配置

### 配置并创建data目录


### odoo日志配置
在odoo.conf里面加上
```sh
log_level = warn
logfile = /opt/odoo/odoo10/odoo10.log
```
重启odoo服务
```sh
# systemctl restart odoo10.service
```

### Odoo配置安全项
启用web数据库管理密码 admin_passwd（不能设置为admin，无效密码）
```sh
admin_passwd = 123456
```
监听IP段改为本地（127.0.0.1）, 可实现禁止外网直接访问odoo（默认0.0.0.0）
```sh
xmlrpc_interface = 127.0.0.1
```
监听端口（默认8069）
```sh
xmlrpc_port = 8069
```

### 增加workers
使用下面命令
```sh
import multiprocessing
multiprocessing.cpu_count()
```

获得cpu个数+1 为workers个数

更多请看本站 odoo性能调优介绍

### 添加自己的插件
mkdir /opt/odoo/odoo10/myaddons
addons_path = /opt/odoo/odoo10/myaddons

> 这里没有配置addons_path参数，因为odoo是以包的形式安装的，
所以目录为 /opt/odoo/odoo10/venv/lib/python2.7/site-packages/odoo-10.0.post20170408-py2.7.egg/odoo/addons。
此版本的odoo把核心模块和业务模块都放在一个目录


启动服务器测试
```sh
(venv) odoo@$ odoo -c odoo10.conf
```
浏览器访问 http://Server_IP:8069

## 二，配置开机启动脚本


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