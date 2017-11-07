# Odoo部署

## 一，说明

服务器系统为Ubuntu 16.04.3 LTS 64位。不推荐使用Windows部署线上环境，因为其不支持odoo多进程work功能
安装方式为源码，使用版本应该和调试环境一致。如果开发用的github源码，可以通过git log找到最后一天更新的日期例如（20170802），
然后选择一个相同的日期（20170802）的源码压缩包下载（避免修复的bug不一致）。选择tar.gz压缩版本下载，这个源码包不包含git相关记录，所以比较小。
可以在下面的地址下载到odoo官方每日打包的版本。
[打包版本下载地址](https://nightly.odoo.com/10.0/nightly/src/)

## 二，步骤
### 1，首先创建一个odoo用户，参照之前的分区方案，把odoo放在home中
```sh
# useradd -m -d /home/odoo -U -s /bin/bash odoo 
```
设置密码
```sh
# passwd odoo
```
### 2，安装python及依赖库
#### python2.7
```sh
# apt-get install python
```
#### python编译依赖
> 不推荐使用deb方式安装python依赖包，可能会因为依赖库版本问题导致一些bug。

```sh
# apt-get install build-essential python-dev python-setuptools -y
```
#### 如果没有pip使用easy_install安装pip

> python-pip版本比较老，所以这里使用easy_install安装。
> 如果是阿里云，系统镜像自动安装了pip，可跳过此步骤，否则可能导致No module named urllib3问题。

```sh
# easy_install pip
```
#### lxml依赖
```sh
# apt-get install zlib1g-dev libxslt1-dev libxml2-dev -y
```
#### ldap依赖
```sh
# apt-get install libsasl2-dev libldap2-dev libssl-dev -y
```
#### pillow 依赖库
```sh
# apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev libfreetype6-dev liblcms2-dev libwebp-dev -y
```
#### postgresql 依赖库
```sh
apt-get install libpq-dev -y
```
#### virtualenv
```sh
# pip install virtualenv
```
### 3，安装postgresql数据库

> ubuntu16.04的postgresql是9.5的, 建议开发和部署使用相同版本。
> 需要安装不同版本的postgresql可以参考https://devecho.com/v/postgresql-basic/

```sh
# apt-get install postgresql-9.5 postgresql-server-dev-9.5
```

> 安装完postgresql数据库后，数据库会自动创建postgres系统用户，在数据库内也会有同名的数据库用户，可以更改这个用户的密码

```sh
# passwd postgres
```
进入数据库更改密码，创建用户odoo
```js
# su postgres psql
//修改密码
postgres=# ALTER USER postgres WITH PASSWORD 'pg***1';
//建立用户
postgres=# create user "odoo" with password 'odoo***1' createdb;
postgres=# \q
```
### 4，安装nodejs
```shell
# curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
# apt-get install -y nodejs
# npm install -g less less-plugin-clean-css
```

### 5，安装打印程序wkhtmltopdf和中文字体
#### wkhtmltopdf

> ubuntu 14~16 （trusty代表1404的版本号）
> 由于wkhtmltopdf没有提供1604的deb安装包，我们可以用1404的替代

```shell
# wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.2.1/wkhtmltox-0.12.2.1_linux-trusty-amd64.deb
# dpkg -i wkhtmltox-0.12.2.1_linux-trusty-amd64.deb
```
依赖报错
![](https://devecho.com/media/markdownx/6f990fbe-391c-4637-a844-2717f8d19902.png)

使用apt-get强制安装解决
```shell
# apt-get -f install -y
```
#### 中文字体
```shell
# apt-get install ttf-wqy-microhei ttf-wqy-zenhei  -y
```
#### 验证
可以在命令行中使用wkhtmltopdf来验证wkhtmltopdf是否正确工作
![](https://devecho.com/media/markdownx/32004dc3-5fa9-4238-a91f-185ca4b2e0a1.jpg)
如果出现cannot connect to X server错误，可以参考下面链接在linux服务上安装一个X环境
https://stackoverflow.com/questions/9604625/wkhtmltopdf-cannot-connect-to-x-server

### 6，安装odoo
#### 建立安装目录
```shell
root# su odoo
odoo@$ cd /home/odoo
odoo@$ mkdir odoo10
odoo@$ cd odoo10
```
#### 使用virtualenv创建虚拟环境
```shell
odoo@$ virtualenv venv
source venv/bin/activate
```
#### 下载odoo nightly包

> 因为odoo10的版本已经稳定，改动不大，所以就使用一个固定包：odoo_10.0.20171108.tar.gz（11月8日下载）
> 也可以在Windows里面下载，然后使用odoo用户名登录WinSCP软件传入 odoo_10.0.20171108.tar.gz，解压缩

```shell
(venv) odoo@$ wget https://nightly.odoo.com/10.0/nightly/src/odoo_10.0.20171108.tar.gz
(venv) odoo@$ tar xf odoo_10.0.20171108.tar.gz
(venv) odoo@$ cd odoo-10.0-20171108
```
#### 安装odoo python模块

> 这里必须先安装requirements.txt，因为odoo的源码中，setup.py依赖没有版本号，导致安装的python依赖和odoo requirements.txt里版本不一致。

```shell
(venv) pip install -r requirements.txt -i https://pypi.douban.com/simple
```
#### 安装odoo包

> 这里把odoo src包作为python的包安装到venv的lib里去，安装完成后删除整个目录。此时多出一个odoo命令，你可以通过这个命令来启动odoo项目。
> nightly的src包和github的目录结构略有不同(根目录无odoo-bin等脚本)，适合部署安装用，开发仍然推荐使用git的。


```shell
(venv) odoo@$ python setup.py install 
(venv) odoo@$ cd ..
(venv) odoo@$ rm -rf odoo-10.0-20171108  
```



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

开始前先update ，是同步 /etc/apt/sources.list 和 /etc/apt/sources.list.d 中列出的源的索引，这样才能获取到最新的软件包
```sh
# apt-get update 
```
更新一下系统源。upgrade 是升级已安装的所有软件包，升级之后的版本就是本地索引里的，因此，在执行 upgrade 之前一定要执行

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