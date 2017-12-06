# Odoo安装

> 服务器操作系统为Ubuntu 16.04.3 LTS 64位。不推荐使用Windows部署线上环境，因为其不支持odoo多进程work功能

## 1，首先创建一个odoo用户，参照之前的分区方案，把odoo放在home中
```sh
# useradd -m -d /home/odoo -U -s /bin/bash odoo
```
> -m option ensures its home directory is created
>
> -d参数指定user的home目录
>
> -s参数指定使用的shell
>
> `-g sudo` adds it to the sudoers list so it can run commands as root
>
> -U,Create a group with the same name as the user, and add the user to this group.


设置密码
```sh
# passwd odoo
```
暂定odoo***1

## 2，安装python及依赖库
### python2.7
```sh
# apt-get install python
```
### python编译依赖
> 不推荐使用deb方式安装python依赖包，可能会因为依赖库版本问题导致一些bug。

```sh
# apt-get install build-essential python-dev python-setuptools -y
```
### 如果没有pip使用easy_install安装pip

> python-pip版本比较老，所以这里使用easy_install安装。
> 如果是阿里云，系统镜像自动安装了pip，可跳过此步骤，否则可能导致No module named urllib3问题。

```sh
# easy_install pip
```
### lxml依赖
```sh
# apt-get install zlib1g-dev libxslt1-dev libxml2-dev -y
```
### ldap依赖
```sh
# apt-get install libsasl2-dev libldap2-dev libssl-dev -y
```

### pillow 依赖库
```sh
# apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev libfreetype6-dev liblcms2-dev libwebp-dev -y
```
### postgresql 依赖库
```sh
# apt-get install libpq-dev -y
```
### virtualenv
```sh
# pip install virtualenv
```
## 3，安装postgresql数据库
## test-eng lish
> ubuntu16.04的postgresql是9.5的, 建议开发和部署使用相同版本。
> 需要安装不同版本的postgresql可以参考https://devecho.com/v/postgresql-basic/

```sh
# apt-get install postgresql-9.5 postgresql-server-dev-9.5
```

> 安装完postgresql数据库后，数据库会自动创建postgres系统用户，密码为随机，设为pgyto1，home目录为/var/lib/postgresql
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

## 4，安装nodejs
```sh
# curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
# apt-get install -y nodejs
# npm install -g less less-plugin-clean-css
```

## 5，安装打印程序wkhtmltopdf和中文字体
### wkhtmltopdf

> ubuntu 14~16 （trusty代表1404的版本号）
> 由于wkhtmltopdf没有提供1604的deb安装包，我们可以用1404的替代

```sh
# wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.2.1/wkhtmltox-0.12.2.1_linux-trusty-amd64.deb
# dpkg -i wkhtmltox-0.12.2.1_linux-trusty-amd64.deb
```
依赖报错
![](https://devecho.com/media/markdownx/6f990fbe-391c-4637-a844-2717f8d19902.png)

使用apt-get强制安装解决
```sh
# apt-get -f install -y
```
### 中文字体
```sh
# apt-get install ttf-wqy-microhei ttf-wqy-zenhei  -y
```
### 验证
可以在命令行中使用wkhtmltopdf来验证wkhtmltopdf是否正确工作
```sh
wkhtmltopdf  www.163.com 163.pdf
```
![](https://devecho.com/media/markdownx/32004dc3-5fa9-4238-a91f-185ca4b2e0a1.jpg)
如果出现cannot connect to X server错误，可以参考下面链接在linux服务上安装一个X环境
https://stackoverflow.com/questions/9604625/wkhtmltopdf-cannot-connect-to-x-server

## 6，安装odoo

安装方式为源码，按照来源不同可以分成两种安装方式
第一种：使用nightly包安装
使用版本应该和调试环境一致。如果开发用的github源码，可以通过git log找到最后一天更新的日期例如（20170802），
然后选择一个相同的日期（20170802）的源码压缩包下载（避免修复的bug不一致）。nightly包不包含git相关记录，所以比较小。
有两个压缩格式供选择，一般选择tar.gz压缩版本下载。
可以在下面的地址下载到odoo官方每日打包的版本。
[nightly包下载地址](https://nightly.odoo.com/10.0/nightly/src/)

第二种：使用github包安装
和开发用的源码相同，包含git相关记录。打开网页后，看好分支，点击DownloadZIP下载
[github包下载地址](https://github.com/odoo/odoo/tree/10.0)

> nightly包和github包的目录结构略有不同，比如根目录无odoo-bin等脚本，还有全部模块都放到了一起。
适合部署安装用，开发仍然推荐使用git的。
> 但因为我们使用E版，希望E版模块和C版自带模块区分开，所以我们部署也用github包，这里存有一个疑问
：？用阿里云做测试，使用github包里面的setup.py进行安装，在venv环境下，看看安装完后具体位置
说白了就是比较nightly包的setup.py和github包的setup.py具体内容

### 建立安装目录
```sh
root# su odoo
odoo@$ cd /home/odoo
odoo@$ mkdir odoo10
odoo@$ cd odoo10
```
### 使用virtualenv创建虚拟环境
```sh
odoo@$ virtualenv venv
odoo@$ source venv/bin/activate
```
### 方法一，使用nightly包安装

#### 下载nightly包

> 因为odoo10的版本已经稳定，改动不大，所以就使用一个固定包：odoo_10.0.20171108.tar.gz（11月8日下载）

```sh
(venv) odoo@$ wget https://nightly.odoo.com/10.0/nightly/src/odoo_10.0.20171108.tar.gz
(venv) odoo@$ tar xf odoo_10.0.20171108.tar.gz
(venv) odoo@$ cd odoo-10.0-20171108
```

> 也可以在Windows里面下载，然后使用odoo用户名登录WinSCP软件传入 odoo_10.0.20171108.tar.gz，解压缩

#### 安装odoo python模块

> 这里必须先安装requirements.txt，因为odoo的源码中，setup.py依赖没有版本号，导致安装的python依赖和odoo requirements.txt里版本不一致。

```sh
(venv) odoo@$ pip install -r requirements.txt -i https://pypi.douban.com/simple
```
中间会安装报错：Failed building wheel for python-ldap，执行下面这个指令安装ldap参考
[ldap Ubuntu](https://stackoverflow.com/questions/4768446/i-cant-install-python-ldap)
其实上面已经装过

```sh
(venv) odoo@$ apt-get install libsasl2-dev python-dev libldap2-dev libssl-dev
```

#### 安装odoo包

> 这里把odoo src包作为python的包安装到venv的lib里去，安装完成后删除整个目录。此时多出一个odoo命令，你可以通过这个命令来启动odoo项目。 

```sh
(venv) odoo@$ python setup.py install 
(venv) odoo@$ cd ..
(venv) odoo@$ rm -rf odoo-10.0-20171108  
```
### 方法二，使用github包安装

#### 下载github包
使用git clone 命令取得github包
```sh
(venv) odoo@$ git clone https://github.com/odoo/odoo.git --depth 1 --branch 10.0 --single-branch odoo10
```

> 也可以在Windows里面下载odoo-10.0.zip，解压缩。然后使用odoo用户名登录WinSCP软件传入所有文件到/home/odoo/odoo10中

其中比较重要的几个文件和目录分别是：
1.  addons odoo主要业务与功能模块，odoo10中现有263个。
1.  odoo odoo核心框架代码
1.  odoo-bin odoo项目启动文件
1.  requirements.txt python项目依赖说明文件

#### 安装odoo python模块

> 这里必须先安装requirements.txt，因为odoo的源码中，setup.py依赖没有版本号，导致安装的python依赖和odoo requirements.txt里版本不一致。

```sh
(venv) odoo@$ pip install -r requirements.txt -i https://pypi.douban.com/simple
```
报错参照上面

#### 安装odoo包
> 与上面的安装方式不同，不再需要执行Setup步骤，odoo-bin 可以直接启动


[back](../)
