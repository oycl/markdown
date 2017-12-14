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
* **Module addons** are the building blocks for Odoo applications. A module can add new features to Odoo, or modify existing ones. It is a directory containing a manifest, or descriptor file, named \__manifest__.py , plus the remaining files that implement its features.
* **Applications** are the way major features are added to Odoo. They provide the core elements for a functional area, such as Accounting or HR, based on which additional addon modules modify or extend features. Because of this, they are highlighted in the Odoo Apps menu.

#### Modifying and extending modules

As a general rule, it's considered a bad practice to modify existing modules by changing their source code directly.This is especially true for the official modules provided by Odoo.

Instead, we should create the extension modules to be installed next to the modules we want to modify,implementing the changes we need.

#### Creating the module basic skeleton

Odoo includes a `scaffold` command to automatically create a new module directory, with a basic structure already in place.You can learn more about it with the following command:
```sh
$~/odoo-dev/odoo/odoo-bin scaffold --help
```

* An Odoo addon module is a directory containing a \__manifest__.py descriptor file.
	The content is:
	```xml
	{
		'name':'To-Do Application',
		'description':'Manage your personal To-Do tasks.',
		'author':'Daniel Reis',
		'depends':['base'],
		'application':True,
	}
	```

	`depends`attribute can have a list of other modules that are required. Odoo will have them automatically installed when this module is installed.

	`summary` is displayed as a subtitle for the module.

	`version`, by default, is 1.0. It should follow semantic versioning rules [see](http://semver.org/ for details).

	`license` identifier, by default is LGPL-3.

	`website` is a URL to find more information about the module. This can help people find more documentation or the issue tracker to file bugs and suggestions.

	`category` is the functional category of the module, which defaults to Uncategorized. The list of existing categories can be found in the security groups form ( Settings \| User \| Groups), in the Application field drop-down list.

	`installable` is by default True but can be set to False to disable a module.

	`auto_install` if set to True , this module will be automatically installed, provided all its dependencies are already installed. It is used for the glue modules.

* It also needs to be Python importable, so it must also have a \__init__py file
* The module's directory name is its technical name.We will use todo_app for it.The technical name must be a valid Python identifier:it should begin with a letter and can only contain letters, numbers, and the underscore character.


#### A word about licenses
LGLP和AGPL的区别

#### Adding to the addons path

#### Installing the new module
In the **Apps** top menu, select the **Update Apps List** option. This will update the module list, adding any modules that may have been added since the last update to the list. Remember that we need the developer mode enabled for this option to be visible. That is done in the **Settings** dashboard, in the link at the bottom-right, below the Odoo version number information.

The **Apps** option shows us the list of available modules. By default, it shows only the application modules. Since we have created an application module, we don't need to remove that filter to see it. Type todo in the search and you should see our new module, ready to be installed:

Now click on the module's **Install** button and we're ready!

#### Upgrading a module

Developing a module is an iterative process, and you will want changes made on source files to be applied to and made visible in Odoo.

In many cases, this is done by upgrading the module: look up the module in the **Apps** list and once it is already installed, you will have an **Upgrade** button available.

However, when the changes are only in Python code, the upgrade may not have an effect. Instead of a module upgrade, an application server restart is needed. **Since Odoo loads Python code only once, any later changes to code require a server restart to be applied.**

In some cases, if the module changes were in both data files and Python code, **you might need both the operations.** This is a common source of confusion for new Odoo developers.

幸运的是我们可以在终端里使用 **-u** 选项来完成升级模块操作。也就是重启Odoo后，不用再去点击 **升级** 按钮了

```sh
$ ./odoo-bin -d todo -u todo_app
```
但是更新模块清单和卸载模块并没有对应的指令。


#### The server development mode
In Odoo 10 a new option was introduced providing developer-friendly features. To use it start the server instance with the additional option --dev=all.

This enables a few handy features to speed up our development cycle. The most important are:
* Reload Python code automatically, once a Python file is saved, avoiding a manual server restart
* Read view definitions directly from the XML files, avoiding manual module upgrades

The --dev option accepts a comma-separated list of options, although the all option will be suitable most of the time. We can also specify the debugger we prefer to use. By default the Python debugger, pdb, is used. Some people might prefer to install and use alternative debuggers. Here also supported are ipdb and pudb.


### The model layer
Models describe business objects, such as an opportunity, sales order, or partner (customer,supplier, and so on). A model has a list of attributes and can also define its specific business.

Models are implemented using a Python class derived from an Odoo template class. They translate directly to database objects, and Odoo automatically takes care of this when installing or upgrading the module. The mechanism responsible for this is the Object Relational Model (ORM).

Our module will be a very simple application to keep to-do tasks. These tasks will have a single text field for the description and a checkbox to mark them as complete. We should later add a button to clean the to-do list of the old, completed tasks.

#### Creating the data model

Let's create a todo_model.py file in the main directory of the todo_app module.Add the following content to it:

```python
# -*- coding: utf-8 -*-
from odoo import models, fields
class TodoTask(models.Model):
	_name = 'todo.task'
	_description = 'To-do Task'
	name = fields.Char('Description', required=True)
	is_done = fields.Boolean('Done?')
	active = fields.Boolean('Active?', default=True)

```

The first line is a special marker telling the Python interpreter that this file has UTF-8 so that it can expect and handle non-ASCII characters. We won't be using any, but it's a good practice to have it anyway.

The second line is a Python code import statement, making available the models and fields objects from the Odoo core.

The third line declares our new model. It's a class derived from models.Model.

The next line sets the \_name attribute defining the identifier that will be used throughout Odoo to refer to this model. Note that the actual Python class name, TodoTask in this case, is meaningless to other Odoo modules. The \_name value is what will be used as an identifier.
Notice that this and the following lines are **indented** . If you're not familiar with Python, you should know that this is important: indentation defines a nested code block, so these four lines should all be equally indented.

Then we have the \_description model attribute. It is not mandatory, but it provides a user friendly name for the model records, that can be used for better user messages.

The last three lines define the model's fields. It's worth noting that **name and active are special field names**.
* By default, Odoo will use the name field as the record's title when referencing it from other models.
* The active field is used to inactivate records, and by default, only active records will be shown. We will use it to clear away completed tasks without actually deleting them from the database.


Right now, this file is not yet used by the module. We must tell Python to load it with the module in the \__init__.py file. Let's edit it to add the following line:
```python
from . import todo_model
```

If everything goes right, it is confirmed that the model and fields were created. If you can't see them here, try a server restart with a module upgrade, as described before.

We can also see some additional fields we didn't declare. These are reserved fields Odoo automatically adds to every new model. They are as follows:
* id is a unique numeric identifier for each record in the model.
* create_date and create_uid specify when the record was created and who created it respectively.
* write_date and write_uid confirm when the record was last modified and who modified it respectively.
* \__last_update is a helper that is not actually stored in the database. It is used for concurrency checks.


#### Adding automated tests
The test code files should have a name starting with test_ and should be imported from tests/__init__.py . But the tests directory (or Python submodule) should not be imported from the module's top __init__.py , since it will be automatically discovered and loaded only when tests are executed.


Now add the actual test code, available in the tests/test_todo.py file:
```python
# -*- coding: utf-8 -*-
from odoo.tests.common import TransactionCase
class TestTodo(TransactionCase):
	def test_create(self):
		"Create a simple Todo"
		Todo = self.env['todo.task']
		task = Todo.create({'name': 'Test Task'})
		self.assertEqual(task.is_done, False)
```

This adds a simple test case to create a new to-do task and verifies that the Is Done? field has the correct default value.

Now we want to run our tests. This is done by adding the `--test-enable` option while installing the module:
```sh
$ ./odoo-bin -d todo -i todo_app --test-enable
```

The Odoo server will look for a tests/ subdirectory in the upgraded modules and will run them. If any of the tests fail, the server log will show that.如果运行成功，log会输出
```sh
INFO odoo10 odoo.addons.todo_app.tests.test_todo: OK
```
但是在数据库中并没有发现一条记录，也只是测试


### The view layer
The view layer describes the user interface. Views are defined using XML, which is used by the web client framework to generate data-aware HTML views.

The Odoo development guidelines states that the XML files defining the user interface should be placed inside a views/ subdirectory.

#### Adding menu items
Create the views/todo_menu.xml file to define a menu item and the action performed by it:
```xml
<?xml version="1.0"?>
<odoo>
<!-- Action to open To-do Task list -->
<act_window id="action_todo_task"
	name="To-do Task"
	res_model="todo.task"
	view_mode="tree,form" />

<!-- Menu item to open To-do Task list -->
<menuitem id="menu_todo_task"
	name="Todos"
	action="action_todo_task" />
</odoo>
```
The user interface, including menu options and actions, is stored in database tables.
* The &lt;act_window&gt; element defines a client-side window action that will open the todo.task model with the tree and form views enabled, in that order.在 **数据库表ir_act_window**里面会生成一条记录，记录视图模式tree,form等，参考模型res_model等等
* The &lt;menuitem&gt; defines a top menu item calling the `action_todo_task` action, which was defined before.在 **数据库表ir_ui_menu**里面会生成一条记录,记录上级菜单，菜单图标等等

	* IR=Information Repository 信息资源库
	* RES=Resource 资源库

> 在Odoo中有两种类型的数据，IR用来代表odoo用来工作的一些参数，比如menus,windows,views,wizards,database tables等等。
> 而RES代表某种存储在odoo中的真实世界对象，比如partner,products,或者账户交易记录等

**The XML file is a data file used to load those definitions into the database when the module is installed or upgraded.**

Both elements include an id attribute. **This id attribute also called an XML ID, is very important:it is used to uniquely identify each data element inside the module, and can be used by other elements to reference it.**
In this case, the &lt;menuitem&gt; element needs to reference the action to process, and needs to make use of the &lt;act_window&gt; ID for that. XML IDs are discussed in greater detail in Chapter 4, Module Data .

Add this attribute to the manifest's dictionary:
```xml
'data': ['views/todo_menu.xml'],
```

Now we need to upgrade the module again for these changes to take effect.

> If an upgrade fails because of an XML error, don't panic! Comment out the last edited XML portions or remove the XML file from \__manifest__.py and repeat the upgrade. The server should start correctly. Now read the error message in the server log carefully: it should point you to where the problem is.

Odoo supports several types of views, but the three most important ones are: **tree (usually called list views), form , and search views**. We'll add an example of each to our module.


#### Creating the form view

Add this new views/todo_view.xml file to define our form view:

```xml
<?xml version="1.0"?>
<odoo>
<record id="view_form_todo_task" model="ir.ui.view">
	<field name="name">To-do Task Form</field>
	<field name="model">todo.task</field>
	<field name="arch" type="xml">
	<form string="To-do Task">
		<group>
			<field name="name"/>
			<field name="is_done"/>
			<field name="active" readonly="1"/>
		</group>
	</form>
	</field>
</record>
</odoo>
```

 **在数据库表ir.ui.view**里面会生成记录.记录的 identifier 是 `view_form_todo_task`. The view is for the `todo.task` model and is named `To-do Task Form`. The name is just for information; it does not have to be unique, but it should allow one to easily identify which record it refers to.

The most important attribute is arch, and it contains the view definition, highlighted in the XML code above. The &lt;form&gt; tag defines the view type, and in this case, contains three fields. We also added an attribute to the `active` field to make it read only.

#### Business document form view

Odoo has a presentation style that mimics a paper page. This form contains two elements: 
&lt;header&gt; to contain action buttons
&lt;sheet&gt; to contain the data fields.

We can now replace the basic &lt;form&gt; defined in the previous section with this one:
```xml
	<form string="To-do Task">
        <header>
		<!-- Buttons go here-->
		</header>
		<sheet>
            <group>
                <field name="name"/>
                <field name="is_done"/>
                <field name="active" readonly="1"/>
            </group>
		</sheet>
	</form>

```

#### Adding action buttons
Forms can have buttons to perform actions. These buttons are able to run window actions such as opening another form or run Python functions defined in the model.
They can be placed anywhere inside a form, but for document-style forms, the recommended place for them is the &lt;header&gt; section.

```xml
<header>
	<button name="do_toggle_done" type="object"
	string="Toggle Done" class="oe_highlight" />
	<button name="do_clear_done" type="object"
	string="Clear All Done" />
</header>
```
The basic attributes of a button comprise the following:
* string with the text to display on the button
* type of action it performs
* name is the identifier for that action
* class is an optional attribute to apply CSS styles, like in regular HTML

#### Using groups to organize forms
The &lt;group&gt; tag allows you to organize the form content. Placing &lt;group&gt; elements inside a &lt;group&gt; element creates a two column layout inside the outer group. 
Group elements are advised to have a name attribute so that its easier for other modules to extend them.

We will use this to better organize our content. Let's change the &lt;sheet&gt; content of our form to match this:
```xml
<sheet>
<group name="group_top">
	<group name="group_left">
		<field name="name"/>
	</group>
	<group name="group_right">
		<field name="is_done"/>
		<field name="active" readonly="1"/>
	</group>
</group>
</sheet>
```

#### The complete form view
> Remember that, for the changes to be loaded to our Odoo database, **a module upgrade is needed**.To see the changes in the web client, the form needs to be **reloaded**: either click again on the menu option that opens it or reload the browser page (F5 in most browsers).

> The action buttons won't work yet since we still need to add their business logic.


#### Adding list and search views
When viewing a model in list mode, a &lt;tree&gt; view is used. Tree views are capable of displaying lines organized in **hierarchies**, but most of the time, they are used to display plain lists.

We can add the following tree view definition to todo_view.xml:
```xml
<record id="view_tree_todo_task" model="ir.ui.view">
	<field name="name">To-do Task Tree</field>
	<field name="model">todo.task</field>
	<field name="arch" type="xml">
		<tree colors="decoration-muted:is_done==True">
			<field name="name"/>
			<field name="is_done"/>
		</tree>
	</field>
</record>

```


### The business logic layer


### Setting up access security


### Better describing the module


### Summary





[back](../)
