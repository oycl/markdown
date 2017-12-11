# Odoo 10 Development Essentials Notes

## Odoo 10 Development Essentials
权利声明，免责声明，商标说明

## Credits
职责分工表

## Foreword
1. Odoo特性：极简化，模块化，可扩展，可伸缩

1. 最新特性：Website特性，和第三方系统的集成机制

## About the Author
作者

## About the Reviewer
评论者

## www.PacktPub.com

### Why subscribe?
出版网站和订阅优惠

## Preface
前言，odoo介绍，本书介绍，章节安排

### What this book covers

* Chapter 1,[Odoo开发环境安装，如何管理Odoo服务器实例](#1getting-started-with-odoo-development)
* Chapter 2,[建立你的第一个Odoo应用](#)
* Chapter 3,[继承-扩展现有的应用](#)
* Chapter 4,[模块数据-扩展标识符概念和数据导入导出](#)
* Chapter 5,[模型-组织应用数据](#)
* Chapter 6,[视图-设计用户界面](#)
* Chapter 7,[ORM应用逻辑-支持商业流程](#)
* Chapter 8,[书写测试和排错代码](#)
* Chapter 9,[Qweb和Kanban视图](#)
* Chapter10,[建立QWeb报告](#)
* Chapter11,[建立Website前端特性](#)
* Chapter12,[扩展API-和其它系统集成](#)
* Chapter13,[部署检查清单](#)

### What you need for this book

书中推荐在虚拟机中的Ubuntu系统中安装Odoo，然后使用自有的系统和软件编程
，具有Python或者XML知识最好，还会使用Linux的命令行

### Who this book is for

目标是拥有商业应用程序开发经验的程序员，最好有MVC设计模式和python语言知识，熟悉HTML，CSS，和JavaScript更有帮助。

### Conventions

新术语和重要的词加粗，代码作为一个块，Shell命令前有$

### Reader feedback

读者反馈

### Customer support

读者支持

#### Downloading the example code
#### Downloading the color images of this book
#### Errata
#### Piracy
#### Questions

## 1,Getting Started with Odoo Development
Odoo is built using the Python programming language,and it uses the PostgreSQL database for data storage;these are the two main requirements of an Odoo host.

### setting up a host for the Odoo server

#### Provision for a Debian host

本书不建议在Windows下搭建环境，但是现实中在pycharm中调试有更高的效率

#### Creating a user account for Odoo

在root下工作被认为是一个坏习惯。据说，Odoo server 不会被root启动。但是，以我的实际使用效果看，没有root权限会很烦。具体内容详见[Odoo安装](OdooInstall.md#1创建odoo用户)

### Installing Odoo from the source

1. 更新一下系统
> 详见[Linux系统环境配置](SetupLinuxEnvironment.md#2update)

2. 安装必要软件包
> 详见[Linux系统环境配置](SetupLinuxEnvironment.md#3安装vimunzipgitnpmsudo)

3. 安装nodejs
> 参照[Odoo安装](OdooInstall.md#4安装nodejs)，这里我们使用的指令有所不同

	```sh
	sudo ln -s /usr/bin/nodejs /usr/bin/node  # call node runs nodejs
	sudo npm install -g less less-plugin-clean-css  # Install less compiler
	```
	> 从9.0版本开始Odoo web client需要less Css 预处理，为了安装这个我们需要Node.js和npm

4. 安装odoo
```sh
# Install Odoo from source
mkdir ~/odoo-dev  # Create a directory to work in
cd ~/odoo-dev  # Go into our work directory
git clone https://github.com/odoo/odoo.git -b 10.0 --depth=1  # Get Odoo source code
./odoo/setup/setup_dev.py setup_deps  # Install Odoo system dependencies
./odoo/setup/setup_dev.py setup_pg  # Create PostgreSQL superuser for this Unix user
```

	> 与我们手动的安装方式不同，上面的命令使用了python来运行shell命令
	>
	> 这里是我们的手动安装方法：[Odoo安装](OdooInstall.md#6安装odoo)

5. 运行
```sh
$~/odoo-dev/odoo/odoo-bin
```
	> As a developer, we will need to work with several databases, so it's more convenient to create
them from the command line, so we will learn how to do this. Now press Ctrl + C in the terminal
to stop the Odoo server and get back to the command prompt.

### Initializing a new Odoo database

> To be able to create a new database, your user must be a PostgreSQL superuser. The following command creates a PostgreSQL superuser for the current Unix user:

	sudo createuser --superuser ${whoami}

> To create new database, use `createdb` command. Let's create a `demo` database

	createdb demo
> To initialize this database with the Odoo data schema,we should run odoo on the empty database using the `-d` option. This will take a couple of minutes to initialize a demo database, and it will end with an INFO log message,**Modules loaded**.

	~/odoo-dev/odoo/odoo-bin -d demo

> By default, this will initialize the database with demonstration data, which is often useful for development databases. To initialize a database without demonstration data, add the --withoutdemo-data=all option to the command.
>
> the default administrator account is `admin` with its password `admin`

#### Managing your databases
```sh
# createdb --template=demo demo-test
# psql -l
# dropdb demo-test
```

> In fact,every time we create a database, a template is used. If none is specified, a predefined one called template1 is used.

### A word about Odoo product versions
Odoo10不能使用之前版本软件建立的数据库。模块也遵循这个原则，下载模块的时候就要注意版本

9.0和10.0版本要保持`API Stable`,这样用户模块就不会因为不兼容的改变而不能运行

master分支将会产生下一个版本，但要注意到下一个版本已经不是`API Stable`

### More server configuration options
$./odoo-bin --help 没有经过安装，应该不能使用

#### Odoo server configuration files
Odoo默认使用`.odoorc`文件作为配置文件，位置在home目录。

我们使用conf文件，参照：[Odoo项目配置](OdooConfig.md)。可以使用save命令产生.odoorc文件。

```sh
$ ~/odoo-dev/odoo/odoo-bin --save --stop-after-init #save configuration to file
```
> the --stop-after-init option to stop the server after it finishes its actions.This option is often used when running tests or asking to run a module upgrade to check whether it is installed correctly.

还可以在启动的时候使用`-c`指定配置文件，我们就是指定`odoo10.conf`作为配置文件

#### Changing the listening port
	在多实例情况下，可以使用`xmlrpc_port`配置不同的端口提供服务

#### The database filter option
For example, to allow only the demo database we would use this command:
	```sh
	$ ~/odoo-dev/odoo/odoo-bin --db-filter=^demo$
	```
	
#### Managing server log messages
参照[Odoo项目配置](OdooConfig.md#conf文件内容)

Finally, the --dev=all option will bring up the Python debugger (pdb) when an exception is
raised. It's useful to do a post-mortem analysis of a server error. Note that it doesn't have any effect
on the logger verbosity. More details on the Python debugger commands can be found at [here.](
https://docs.python.org/2/library/pdb.html#debugger-commands .)


### Developing from your workstation

#### Using a Linux text editor
#### Installing and configuring Samba
参照[搭建Samba和Ftp文件服务](OdooSambaFtp.md#samba)

#### Activating the developer tools
The developer tools provide advanced server configuration and features. These include a debug menu in the top menu bar along with additional menu options in the Settings menu, in particular the Technical menu.

The second option, Activate the developer mode (with assets) , also disables the minification of JavaScript and CSS used by the web client, making it easier to debug client-side behavior:

> The Technical menu option allows us to inspect and edit all the Odoo configurations stored in the database, from user interface to security and other system parameters. You will be learning more about many of these throughout the book.

### Installing third-party modules

#### Finding community modules
The Odoo apps store at [here](http://apps.odoo.com).Github上有The Odoo Community Association[(OCA)](https://github.com/OCA/)

如果使用git做版本控制，把下载的模块放到源码里面不是很好，因为我们要和服务器保持同步

使用下面命令得到一个todo的app
```sh
$ git clone https://github.com/dreispt/todo_app.git -b 10.0
```

#### Configuring the addons path
参照[Odoo项目配置](OdooConfig.md#conf文件内容)里面addons_path一项

#### Updating the apps list
For this, we need developer mode enabled, since it provides the **Update Apps List** menu option. It can be found in the **Apps** top menu.

### Summary

In this chapter, we learned how to set up a Debian system to host Odoo and install it from the GitHub source code. We also learned how to create Odoo databases and run Odoo instances. To allow developers to use their favorite tools in their personal workstation, we explained how to configure file sharing in the Odoo host.

We should now have a functioning Odoo environment to work with and be comfortable with managing databases and instances.

With this in place, we're ready to go straight into action. In the next chapter, we will create our first Odoo module from scratch and understand the main elements it involves.

So let's get started!


## 2,Building Your First Odoo Application

Odoo follows an **MVC-like** architecture, and we will go through the layers during our implementation of the To-Do application:
* The model layer, defining the structure of the app's data
* The view layer, describing the user interface
* The controller layer, supporting the business logic of the application

> Note that the concept of the term controller mentioned here is different from the Odoo web development controllers. These are program endpoints that web pages can call to perform actions.


### Essential concepts
#### Understanding applications and modules
* **Module addons** are the building blocks for Odoo applications. A module can add new features to Odoo, or modify existing ones. It is a directory containing a manifest, or descriptor file, named __manifest__.py , plus the remaining files that implement its features.
* **Applications** are the way major features are added to Odoo. They provide the core elements for a functional area, such as Accounting or HR, based on which additional addon modules modify or extend features. Because of this, they are highlighted in the Odoo Apps menu.

#### Modifying and extending modules

#### Creating the module basic skeleton


#### A word about licenses


#### Adding to the addons path

#### Installing the new module

#### Upgrading a module

#### The server development mode



### The model layer


### The view layer


### The business logic layer


### Setting up access security


### Better describing the module


### Summary





[back](../)
