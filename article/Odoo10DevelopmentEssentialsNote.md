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

The next line sets the \_name attribute defining the identifier that will be used throughout Odoo to refer to this model. Note that the actual Python class name, TodoTask in this case, is meaningless to other Odoo modules. The \_name value is what will be used as an identifier.Python类名没什么实际意义，但是_name属性用作标识符，在view里面直接使用todo.task；在数据库里把.转换成下划线_变成数据库表名：todo.task就会变成表todo_task。

> Notice that this and the following lines are **indented** . If you're not familiar with Python, you should know that this is important: indentation defines a nested code block, so these four lines should all be equally indented.

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
* The &lt;menuitem&gt; defines a top menu item calling the `action_todo_task` action, which was defined before.在 **数据库表ir_ui_menu**里面会生成一条记录,记录上级菜单，菜单图标等等。其中的action字段存储了指向ir_act_window表里面主键id的值，也就是点击这个菜单调用哪个ir_act_windows

	* IR=Information Repository 信息资源库
	* RES=Resource 资源库

> 在Odoo中有两种类型的数据，IR用来代表odoo用来工作的一些参数，比如menus,windows,views,wizards,database tables等等。
>
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

**在数据库表ir.ui.view**里面会生成记录。记录的 identifier 是 `view_form_todo_task`. The view is for the `todo.task` model and is named `To-do Task Form`. The name is just for information; it does not have to be unique, but it should allow one to easily identify which record it refers to.

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
* name is the identifier for that action.对应python文件中的函数名
* class is an optional attribute to apply CSS styles, like in regular HTML.这里在第一个按钮里使用了class,观察实际效果为反色高亮

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
这段代码同样会在数据库表ir.ui.view中添加一条XML ID为`view_tree_todo_task`的记录。

This defines a list with only two columns: name and is_done. We also added a nice touch: the lines for done tasks (is_done==True ) are shown grayed out. This is done applying the Bootstrap class muted . [Check](http://getbootstrap.com/css/#helper-classes-colors) for more information on Bootstrap and its contextual colors.但是在企业版里面尝试改几个颜色也没有效果，也许是颜色的关键词用的不对，或者企业版不支持。

继续往todo_view.xml里面添加search view。在数据库表ir.ui.view中添加一条XML ID为`view_filter_todo_task`的记录。
```xml
<!-- To-Do Task Search view -->
<record id="view_filter_todo_task" model="ir.ui.view">
	<field name="name">To-do Task Filter</field>
	<field name="model">todo.task</field>
	<field name="arch" type="xml">
		<search>
		<field name="name"/>
		<filter string="Not Done" domain="[('is_done','=',False)]"/>
		<filter string="Done" domain="[('is_done','!=',False)]"/>
		</search>
	</field>
</record>
```

The &lt;field&gt; elements define fields that are also searched when typing in the search box.如果放置两列，在搜索的时候就会出现两列供选择使用哪一列。

输入一次同时搜索两列，使用下面代码
```xml
<field name="name" string="件号或者名称"
 filter_domain="['|', ('name', 'ilike', self), ('part_name', 'ilike', self)]"/>
```

The &lt;filter&gt; elements add predefined filter conditions, that can be toggled with a user click, defined
using a specific syntax.预置在筛选下面，供直接使用

### The business logic layer
Now we will add some logic to our buttons. This is done with Python code, using the methods in the model's Python class.
#### Adding business logic
First, we need to import the new API, so add it to the import statement at the top of the Python file:
```python
from odoo import models, fields, api
```
The action of the **Toggle Done** button will be very simple: just toggle the **Is Done?** flag. For logic on records, use the @api.multi decorator. Here, **self** will represent a recordset, and we should then loop through each record. **self表示一个记录集**

Inside the TodoTask class, add this:
```python
@api.multi
def do_toggle_done(self):
	for task in self:
		task.is_done = not task.is_done
	return True
```

The code loops through all the to-do task records and, for each one, modifies the is_done field,inverting its value. 事实是在Form视图内，只对本条记录起作用，并不会影响其它记录。而这个按钮也不会出现在list视图里面。

The method does not need to return anything, but we should have it to at least return a True value. The reason is that clients can use XML-RPC to call these methods, and this protocol does not support server functions returning just a None value.

For the **Clear All Done** button, we want to go a little further. It should look for all active records that are done, and make them inactive.

Usually form buttons are expected to act only on the selected record, but in this case, we will want it also act on records other than the current one.这一次确实影响了所有记录的状态。
```python
@api.model
def do_clear_done(self):
	dones = self.search([('is_done', '=', True)])
	dones.write({'active': False})
	return True
```
这里面使用@api.model会出现错误，google的答案是新版本要使用@api.multi

~~On methods decorated with @api.model~~, the self variable represents the model with no record in particular. We will build a dones recordset containing all the tasks that are marked as done.Then, we set the active flag to False on them.

The `search` method is an API method that returns the records that meet some conditions. These conditions are written in a domain, which is a list of triplets. We'll explore domains in more detail in Chapter 6 , Views - Designing the User Interface .

The `write` method sets the values at once on all the elements of the recordset. The values to write are described using a dictionary. **Using `write` here is more efficient than iterating through the recordset to assign the value to each of them one by one.**


#### Adding tests
Now we should add tests for the business logic. Ideally, we want every line of code to be covered by at least one test case. In tests/test_todo.py, add a few more lines of code to the test_create() method:
添加完以后test就变成这样
```python
    def test_create(self):
        "Create a simple Todo"
        Todo = self.env['todo.task']
        task = Todo.create({'name': 'Test Task'})
        self.assertEqual(task.is_done, False)

        "Test Toggle Done"
        task.do_toggle_done()
        self.assertTrue(task.is_done)
        "Test Clear Done"
        Todo.do_clear_done()
        self.assertFalse(task.active)
```
试着理解一下，Todo把指针指向todo.task这个model，Todo有create方法，建立了一条记录task
the self variable represents the model with no record in particular.self测试task这一条记录的is_done是True还是False因为默认建立一条记录的is_done是False的，所以应该通过。我们试着修改False为True，则log返回
```sh
.todo_app.tests.test_todo: `     self.assertEqual(task.is_done, True)
.todo_app.tests.test_todo: ` AssertionError: False != True
.todo_app.tests.test_todo: Ran 1 test in 0.020s
.todo_app.tests.test_todo: FAILED
```


### Setting up access security
You might have noticed that, upon loading, our module is getting a warning message in the server log:
```sh
The model todo.task has no access rules, consider adding one.
```
As a superuser, the `admin` ignores data access rules, and that's why we were able to use the form without errors. But we must fix this before other users can use our model.

Another issue we have yet to address is that we want the to-do tasks to be private to each user.Odoo supports **row-level access rules**, which we will use to implement that.

#### Testing access security
we should change them so that they use the Demo user instead.For this, we should edit the tests/test_todo.py file to add a setUp method:
```python
class TestTodo(TransactionCase):

	def setUp(self, *args, **kwargs):
		result = super(TestTodo, self).setUp(*args, **kwargs)
		user_demo = self.env.ref('base.user_demo')
		self.env= self.env(user=user_demo)
		return result
```
This first instruction calls the setUp code of the parent class. 调父类，不太懂
The next ones change the environment used to run the tests, `self.env` , to a new one using the Demo user. No further changes are needed to the tests we already wrote.

We should also add a test case to make sure that users can see only their own tasks. For this, first, add an additional import at the top:
```python
from odoo.exceptions import AccessError
```
Next, add an additional method to the test class:
```python
def test_record_rule(self):
	"Test per user record rules"
	Todo = self.env['todo.task']
	task = Todo.sudo().create({'name':'Admin Task'})
	with self.assertRaises(AccessError):
		Todo.browse([task.id]).name
```
Since our `env` method is now using the Demo user, we used the `sudo()` method to change the context to the admin user. We then use it to create a task that should not be accessible to the Demo user.

When trying to access this task data, we expect an AccessError exception to be raised. If we run the tests now, they should fail, so let's take care of that.
整个测试语句是先把用户切换到demo用户，然后用admin用户建立一个记录，最后用demo用户访问admin用户的记录，然后会引发错误

#### Adding access control security
use the web client and go to **Settings \| Technical \| Security \| Access Controls List** :
里面显示了对每个模型的访问控制列表，具体到每一个群组对记录允许的行为。这些信息存储在数据库表ir.model.access里面。

We will add full access to the employee group on the model. Employee is the basic access group nearly everyone belongs to.使用一个CSV文件来做这件事:security/ir.model.access.csv
```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
acess_todo_task_group_user,todo.task.user,model_todo_task,base.group_user,1,1,1,1
```
文件名对应数据库表，第一行是列名，解释如下：

* `id` is the record external identifier (also known as XML ID). It should be unique in our module.
* `name` is a description title. It is only informative and it's best if it's kept unique. Official modules usually use a dot-separated string with the model name and the group. Following this convention, we used todo.task.user.
* `model_id` is the external identifier for the model we are giving access to. Models have XML IDs automatically generated by the ORM: for todo.task , the identifier is **model_todo_task**.注意这一条命名原则
* `group_id` identifies the security group to give permissions to. The most important ones are provided by the base module. The Employee group is such a case and has the identifier `base.group_user`.这是组的扩展标识符，在对公司进行分组的时候，要把这个标识符起好，以便后续使用。
* `perm` fields flag the access to grant read , write , create , or unlink (delete) access.

最后还要在 \__manifest__.py 里面加入这一条，看起来是这样的
```xml
'data': [
	'security/ir.model.access.csv',
	'views/todo_view.xml',
	'views/todo_menu.xml',
],
```
If we run our tests now they should only fail the `test_record_rule` test case.

#### Row-level access rules
在 **访问控制列表** 菜单旁边是 **记录列表** 菜单。记录列表是定义在 **数据库表ir_rule** 里面的。

As usual, we need to provide a distinctive name.We also need the model they operate on and the domain filter to use for the access restriction. The domain filter uses the usual list of tuples syntax used across Odoo.也就是说`domain_force`可以过滤出自己创建的记录。

Usually, rules apply to some particular security groups. In our case, we will make it apply to the Employees group. If it applies to no security group, in particular, it is considered global (the global field is automatically set to True ). Global rules are different because they impose restrictions that non-global rules can't override.

To add the record rule, we should create a `security/todo_access_rules.xml` file with the following content:
```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
  <data noupdate="1">
    <record id="todo_task_user_rule" model="ir.rule">
      <field name="name">ToDo Tasks only for owner</field>
      <field name="model_id" ref="model_todo_task"/>
      <field name="domain_force">[('create_uid','=',user.id)]
      </field>
      <field name="groups" eval="[(4,ref('base.group_user'))]"/>
    </record>
  </data>
</odoo>

```
> Notice the noupdate="1" attribute. It means this data will not be updated in module upgrades. This will allow it to be customized later since module upgrades won't destroy user-made changes. But be aware that this will also be the case while developing, so you might want to set noupdate="0" during development until you're happy with the data file.

> In the groups field, you will also find a special expression. It's a one-to-many relational field, and they have a special syntax to operate with. In this case, the (4, x) tuple indicates to append x to the records, and here x is a reference to the Employees group, identified by base.group_user. This one-to-many writing special syntax is discussed in more detail in Chapter 4 , Module Data.

把`security/todo_access_rules.xml`添加到\__manifest__.py里面
```xml
'data': [
	'security/ir.model.access.csv',
	'security/todo_access_rules.xml',
	'views/todo_view.xml',
	'views/todo_menu.xml',
],
```
如果一切顺利，我们添加的测试就可以通过了。
填写一些自己的测试，当这一条规则添加到数据库表ir_rule后，把todo_access_rules.xml从\__manifest__.py里面注释掉，重启服务，则数据库表里面记录会删除，这说明在data里面的文件每次启动都会使用。然后我们用上面提到的`noupdate`来进行控制。
实际上我们只看到了`domain_force`在ir_rule里面有存储，而`group`字段还有待研究，到第四章我们会回来再看一下。


### Better describing the module
图标文件在todo_app/static/description/icon.png里面，注意这个图标是应用的图标不是菜单的图标

### Summary
We created a new module from the start, covering the most frequently used elements in a module:models, the three basic types of views(form, list, and search), business logic in model methods, and access security. In the process, we got familiar with the module development process, which involves module upgrades and application server restarts to make the gradual changes effective in Odoo.
Always remember, when adding model fields, an upgrade is needed. When changing Python code, including the manifest file, a restart is needed. When changing XML or CSV files, an upgrade is needed; also, when in doubt, do both: restart the server and upgrade the modules.
In the next chapter, you will learn how to build modules that will stack on existing ones in order to add features.

## 3,Inheritance - Extending Existing Applications
One of Odoo's most powerful feature is the ability to add features without directly modifying the underlying objects.This is achieved through inheritance mechanisms.

### Adding sharing capabilities to the To-Do app
We will do this with a new module to extend the previously created To-Do app and add these new features using the inheritance mechanisms.

我们将做如下改进
* 扩展task模型，例如哪位用户会对一个任务做出响应
* 改变事物逻辑来操作当前用户的任务，而不是所有能看到的任务
* 扩展views来添加需要的字段
* 添加社交属性：一个信息墙和跟随者

首先建立todo_user/__manifest__.py文件，依赖是todo_app，就是上一章我们建立的app。当依赖todo_app更新时，todo_user也跟着更新，这对于继承机制能否工作来说是必要和重要的

### Extending models
**_inherit**关键字继承了父模型的所有特性，我们只需要声明我们引入的更改
事实上，odoo的model使用了一种集中注册机制。在这种机制中，我们使用self.env[<model name>]来访问。当修改一个model时，我们得到其注册类的引用，然后修改它。这意味着在所有使用这个新model的地方修改都是生效的。

当odoo server启动时，module读取的顺序是有讲究的，这也就是使用依赖的目的。


#### Adding fields to a model
我们会在todo.task模型上添加user responsible和deadline date两个字段

分别在顶层目录和models目录里添加\__init.py__文件，顶层目录的导入models目录名。models目录里面的导入里面的py文件名，如果有很多文件就都写在这里，每行一个。

然后建立todo_task.py文件
```python
# -*- coding: utf-8 -*-
from odoo import models, fields, api
class TodoTask(models.Model):
	_inherit = 'todo.task'
	user_id = fields.Many2one('res.users','Responsible')
	date_deadline = fields.Date('Deadline')
```
> 注意_name名字并没有出现，因为已经从父模型中继承了

**_inherit** 这种继承并没有多建立数据库表，只是在原表上扩充了两个字段，原来数据库的内容依然会保留。
通过这种继承方式，我们可以
* add fields to a model,
* override the definition of fields on a model,
* add constraints to a model,
* add methods to a model,
* override existing methods on a model.
我们继续做一个卸载的测试，我把刚刚扩展的两个字段填上了数据，然后卸载模块，则这两个字段也从数据库表里面消失了。


#### Modifying existing fields
使用和父类里面同名的字段，然后把修改写进去。我们给name字段添加一条提示
```python
name = fields.Char(help="what needs to be done?")
```
这种方式，只修改需要的属性，略过其它没有必要的属性。

#### Modifying model methods
在事物逻辑层面同样有继承机制：添加新类就是在继承类里面声明函数。而扩展或者改变现有逻辑，就是声明相同名字的函数。新的函数将会取代旧的函数，同时也会扩展被继承类的code，使用Python's `super()`方法调用父方法。它可以在`super()`方法前或者后面对原有的逻辑添加新逻辑。

修改函数参数要确保原有的调用能工作正常，事实上，我们需要添加附加参数，使用它们的可选关键字参数（带一个默认值）

原函数Clear All Done因为会清除所有人的task，我们这次修改只清除当前用户的task

```python
    @api.multi
    def do_clear_done(self):
        domain = [('is_done','=',True),'|',('user_id','=',self.env.uid),('user_id','=',False)]
        done_recs = self.search(domain)
        done_recs.write({'active':False})
        return True
```

首先我们设置了过滤条件，这种表达式遵从Odoo-specific语法。这些条件默认使用“与”连接，对于“或”逻辑，使用“|”来连接其后的两个条件。然后使用`search`方法得到记录集来进行操作。

接下来我们重写方法来保留已有的逻辑，我们使用Python`super()`方法来调用父类的方法。使用do_toggle_done()方法为例。
```python
    @api.multi
    def do_toggle_done(self):
        for task in self:
            if task.user_id != self.env.user:
                raise  ValidationError('Only the responsible can do this!')
        return super(TodoTask, self).do_toggle_done()
```
这里我们实际上是给原do_toggle_done()函数套了一层马甲，加了一层判断条件来看是否调用原do_toggle_done()函数。

这里和上一章发现的问题一样，即使使用了multi，函数也不会去搜索整个数据表的记录，只会操作当前form的一条记录。


### Extending views
The `inherit_id` field identifies the view to be extended by referring to its external identifier using the special ref attribute. External identifiers will be discussed in more detail in Chapter 4 ,Module Data.
odoo有一种简洁的写法，可以避免使用XPath语法。但是如果定位使用的字段出现了多次，还是要使用XPath语法。
还有&lt;sheet&gt;,&lt;group&gt;,&lt;div&gt;这些标志也可以被使用，使用它们的name属性来定位。还有CSS的class元素


The position attribute used with the locator element is optional and can have the following values:
* `after` adds the content to the parent element, after the matched node.
* `before` adds the content, before the matched node.
* `inside` (default value) appended the content inside matched node.
* `replace` replaces the matched node. If used with empty content, it deletes an element. Since Odoo 10 it also allows to wrap an element with other markup, by using $0 in the content to represent the element being replaced.
* `attributes` modifies the XML attributes of the matched element. This is done using in the element content <attribute name="attr-name"> elements with the new attribute values to set.

For Example, in the Task form, we have the active field, but having it visible is not that useful.We could hide it from the user. This can be done by setting its invisible attribute:
```xml
<field name="active" position="attributes">
	<attribute name="invisible">1</attribute>
</field>
```
使用隐藏是一个好的习惯，避免因为删除导致其它以来模块出问题。

#### Extending the form view
```xml
<record id="view_form_todo_task_inherited" model="ir.ui.view">
	<field name="name">Todo Task form -User extension</field>
	<field name="model">todo.task</field>
	<field name="inherit_id" ref="todo_app.view_form_todo_task"/>
	<field name="arch" type="xml">
		<field name="name" position="after">
			<field name="user_id"/>
		</field>
		<field name="is_done" position="before">
			<field name="date_deadline"/>
		</field>
		<field name="active" position="attributes">
			<attribute name="invisible">1</attribute>
		</field>
	</field>
</record>
```
> 在数据库表ir.ui.view里面也会产生一条记录，并且inherit_id字段等于被继承视图的id

> 继承视图依然可以被继承，但是会非常复杂，尽量避免。

#### Extending the tree and search views
For the list view, we want to add the `user` field to it:
```xml
<record id="view_tree_todo_task_inherited" model="ir.ui.view">
	<field name="name">Todo Task tree - User extension</field>
	<field name="model">todo.task</field>
	<field name="inherit_id" ref="todo_app.view_tree_todo_task"/>
	<field name="arch" type="xml">
		<field name="name" position="after">
			<field name="user_id"/>
		</field>
	</field>
</record>
```
For the search view, we will add the search by the user and predefined filters for the user's own tasks and the tasks not assigned to anyone:
```xml
<record id="view_filter_todo_task_inherited" model="ir.ui.view">
	<field name="name">Todo Task tree - User extension</field>
	<field name="model">todo.task</field>
	<field name="inherit_id" ref="todo_app.view_filter_todo_task"/>
	<field name="arch" type="xml">
		<field name="name" position="after">
			<field name="user_id"/>
			<filter name="filter_my_tasks" string="My Tasks"
				domain="[('user_id','in',[uid,False])]"/>
			<filter name="filter_not_assigned" string="Not Assigned"
				domain="[('user_id','=',False)]"/>
		</field>
	</field>
</record>

```
### More model inheritance mechanisms
**_inheritance** 可以继承很多父模型，在_inheritance后面加入一个列表。对于这种情况，我们可以使用混合类，混合类是实现了通用特征的模型，以便我们可以其它模型。混合类不希望被直接使用，像一个特性的容器等待被添加到其它模型。

如果我们使用_name属性，起一个不同的名字，我们就可以得到一个新模型，重用了原模型的特性，而不是用数据库表和特性。官方文档管这叫 **prototype inheritance**。可以在这种继承上添加新特性，原模型将不会改变。

还有一种继承叫 **delegation inheritance**,使用`_inherits`属性。允许一个模型包含其它模型使用对观察者来说透明的方法，而在这种情况下每个模型有自己的数据。你扩展一个模型，当你添加新特性时，他们添加到新的模型，原模型不变。在新模型的记录有一个link到原模型，原模型的字段可以直接在新模型使用。

#### Copying features with prototype inheritance
如果使用_name属性指定新名字则新模型会拷贝原模型的特性。拷贝意味着继承到的方法和字段在新模型上是有效的。对字段来说，意味着会被建立和存储在新模型的数据库表上，新模型和原模型的数据再没有关联，只有定义是共享的。
接下来，我们会继承mail.thread的属性到我们的模型。实际中，当我们用到混合时，我们很少从正常的模型继承，因为这会引起数据结构的重复。
Odoo还提供了 **委托继承** 机制来避免数据结构的复制，所以在一般情况下，这种继承使用的更多一些。
#### Embedding models using delegation inheritance
委托继承并不常用，但是它提供了非常便利的解决方案。关键字是 **_inherits** 一个非常好的例子就是odoo的用户模型：res.users，它继承了res.partner模型
```python
class User(models.Model)
	_name = 'res.users'
	_inherits = {'res.partner':'partner_id'}
	partner_id = fields.Many2one('res.partner')
```
使用委托继承，当一个新的用户被创建时，一个partner同样被创建，这个链接保存在用户模型的partner_id里面。这与OO编程中的多态概念类似。
通过委托继承partner模型里的名字和地址可以直接被res.users模型使用，但是数据还是存储在partner模型里面

这种继承的好处是，比较prototype继承，不需要重复的数据结构，例如地址，占用好几个数据表。任何新模型想使用地址信息可以继承partner模型，如果在partner模型里面修改了地址信息，那么其它所有嵌入这个partner模型的地方也会立即生效。

>  注意委托继承只能继承字段，不能继承方法。

#### Adding the social network features
The social network messaging features are provided by the `mail.thread` model of the `mail` module. To add it to a custom model, we need to do the following:
* Have the module depend on `mail`
* Have the class inherit from `mail.thread`
* Have the followers and thread widgets added to the form view
* Optionally, we need to set up record rules for followers.

按照这个清单，通过书，一步一步加进去就行。

`mail.thread`是一个抽象模型。 **抽象模型** 跟普通模型差不多，只是没有数据库里面的表。抽象模型不是被直接使用的，而是希望被用作混合类。我们可以把其当作一个具有特征的模板。为了建立一个抽象类，我们只需要在建立类的时候继承`models.AbstractModel`而不是像普通模型一样继承`models.Model`


### Modifying data
The &lt;record id="x" model="y"&gt; data loading elements actually perform an insert or update operation on the model y : if model x does not exist, it is created; otherwise, it is updated/written over.

Since records in other modules can be accessed using a &lt;model&gt;.&lt;identifier&gt; global identifier, it's possible for our module to overwrite something that was written before by another module.

> Note that since the dot is reserved to separate the module name from the object identifier, it can't be used in identifier names. Instead, use the underscore option.注意，点用来分隔模块名和标识符，就不能用来在标识符内使用，可以用下划线来代替。



#### Modifying menu and action records

#### Modifying security record rules

### Summary
You should now be able to create your own modules by extending the existing ones.

To demonstrate how to do this, we extended the To-Do module we created in the previous chapter, adding new features to the several layers that make up an application.

We extended an Odoo model to add new fields and extended its business logic methods. Next, we modified the views to make the new fields available to them. Finally, we learned how to inherit features from other models and use them to add the social network features to the To-Do app.

The first three chapters gave us an overview of the common activities involved in Odoo development, from Odoo installation and setup to module creation and extension.

The next chapters will each focus on a specific area of Odoo development, most of which we briefly visited in these first chapters. In the following chapter, we will address data serialization and the usage of XML and CSV data files in more detail.

## 4,Module Data
XML和CSV文件在odoo的运行时是不会使用的，只是把它们读入到数据库里面。
### Understanding external identifiers
扩展标识符的概念非常重要，一个原因是更新一个模块，需要检测已经存在的记录，来决定是更新它们还是新建记录;另一个原因是支持内连数据：数据之间的依赖关系。在模块安装的时候，实际数据ID只是一个自增序数。而扩展标识符提供了一种手段来参考相关的记录，不用知道数据库指定的这个ID。Odoo管理从扩展标识符到实际ID的转换。这个机制其实很简单，就在数据库表 **ir.model.data**里面。
在找扩展标识符的时候我们可以按照模块或者模型来寻找
#### Finding external identifiers
一种是使用扩展标识符菜单，另一种是使用查看元数据方法
### Exporting and importing data

#### Exporting data
在listview里面，导出数据是一个标准特性。
> 在Odoo9以后，点击标题会选择全部记录，这对数据记录非常大的情况是非常有用的。
> Odpp会自动导出一个附加 `id` 列，这一列是扩展标识符。如果记录没有指定扩展标识符，一个带有__export__字符取代实际模块名称的标识符会自动产生。
> 在实际使用中，初始迁移的数据每一条记录都指定了扩展标识符，而后来手动输入的记录开始以为不会有标识符，按照书中所说确实在ir.model.data里面搜索到模块module名称为__export__的标识符。而由数据库产生的记录也同样有这种标识符(m.bom)。看来想通过减少ir.model.data记录数量来优化odoo是不可能了。

#### Importing data
就像之前提到的那样，id列提供了唯一标识符。这样就允许已经存在的记录是被更新而不是新建。对于新的记录，我们可以提供一个扩展标识符，或者不填（自动生成）
#### Related records in CSV data files
前面的例子里，user_id是一个`多对一`的关系，列名是user_id/id，值是相关记录的扩展标识符，例如使用`base.user_root`来代表administrator用户

> 使用真实的数据库ID只适用于你在同一个数据库上进行导入导出。通常你会更喜欢使用扩展标识符。
> 如果使用扩展标识符，关联列有/id添加到列名里；如果使用数据库ID，有/.id添加到列名里。而冒号:可以代替斜杠/起到同样效果

实际导出时选择“导入兼容”会比“导出全部数据”少一些列，这些列应该就是关联列。

* 导入兼容:依照form视图的组织形式，只把原始列导出
* 导出全部数据:把关联表的其它字段也加入进来

数据如下
```text
重要度/ID","重要度/重要度名称","重要度"
"1","A级","ultra.I001"
```

同样，多对多的关系一样被支持。一个多对多的典型例子是用户和群组。列名会被添加/id,列值接受被双引号包围的列表，里面是使用逗号分隔的扩展标识符。
```text
"id","name","groups_id/id"
"base.user_demo","Demo User","base.group_no_one,base.group_partner_manager,base.group_user"
"base.user_D009","白荣刚","base.group_yanfa,base.group_no_one,base.group_partner_manager,base.group_user"
```

最后，一对多的关系一样可以通过CSV文件导入。典型例子是一个文档标题有很多行。注意一对多的关系总是多对一关系的反转。还有就是一个公司会有多个银行账户，在下面的例子中我们导入三个银行账户。
```xml
id,name,bank_ids/id,bank_ids/acc_number,bank_ids/state
base.main_company,YourCompany,__export__.res_partner_bank_4,123456789,bank
,,__export__.res_partner_bank_5,135792468,bank
,,__export__.res_partner_bank_6,1122334455,bank
```
实际使用时，需要在银行账户中先建立起银行信息。这种“多”一定是规范的信息，注意使用这种一对多的导入本身不能建立“多”的信息，只能是关联已有“多”的id，再把银行帐号等其它信息填入进去。


### Module data
CSV文件导入的限制是文件名必须匹配模型名，例如使用ir.model.access.csv文件载入ir.model.access模型。

#### Demonstration data
演示数据的意义在于，可以示范模块的使用，还可以建立测试用的数据集。
可以在\__manifest__.py里面使用 `demo`指定一串文件。
每次模块升级，demo文件内容也重新载入

### XML data files
CSV格式提供了一种简单和紧凑格式表示数据，XML文件则提供了对读取过程更强大和更多的控制。文件名也不必匹配模型的名称。这是因为XML格式通过XML元素提供了信息。
之前的章节我们用过XML文件，用户界面组件：视图和菜单，是存储在系统模型里的。在模型中的XML文件就是用来读取记录到服务器的。我们以`data/todo_data.xml`为例演示XML文件的使用。

元素&lt;element&gt;拥有两个强制属性，model和id（就是扩展标识符），然后使用&lt;field&gt; 标志对每个字段进行写。

> 注意的是，斜杠/在XML文件中不能使用，取而代之的是`ref`来引用扩展标识符。我们将会讨论一对多的字段值该如何填写。

#### The data noupdate attribute
当数据重复读取，之前的记录就会被重写。记住：升级模块将来会重写任何手动的更改。尤其是用户在web上修改的视图，如果模型升级将会丢失。正确的过程是对于改变建立继承视图，就像第三章讨论的那样。

这种重复导入的行为是默认的，但是可以被改变，所以当一个模块升级，一些data文件是不被读取的。这就用到了`noupdat="1"`属性来指定。当模块安装的时候执行一次，后面升级就会忽略。

这种特性让我们的手动修改，在模块升级的时候是安全的。 **经常被用到的地方是ACL访问控制列表，因为在系统运行后，难免会添加一些用户和组，然后去修改规则。**

我们还可以使用多个 &lt;data&gt;数据段，有的可以更新，有的不能更新。

在扩展标识符菜单里可以指定是否能够更新。

> noupdate 在开发的时候也很烦人，因为里面的更改不能被体现出来。一个解决方法是使用重新安装指令-i，来代替升级指令-u
。这样重新安装就会体现noupdate里面的更改。

#### Defining records in XML
每个&lt;record&gt;元素有两个基本属性`id`和`model`，然后用&lt;field&gt;指定字段的值。
* id对应扩展标识符
* model对应记录被存入的目标模型

&lt;field&gt;指定值的方式有一点不同，让我们看下面：

##### Setting field values
在&lt;field&gt;中&lt;name&gt;定义了字段，被写入值的内容在&gt;和&lt;/之间。对于时间和日期来说，字符"YYYY-mm-dd" and "YYYY-mm-dd HH:MM:SS"会被妥善处理。但是布尔类型字段任何非空值将被转换为True，"0"和"False"将会被转换为“False”

> 在odoo10上对布尔类型做了改进。因为以前的版本任何非空值包括"0"和"False"都会被转换为真

##### Setting values using expressions
最详细的定义一个值是使用`eval`属性。它会计算一个python表达式，然后指定结果给字段赋值。
表达式除了有Python内建，还有一些附加的标识符可以使用，接下来我们看一看。

如果想处理日期，接下来的模块必须有效：time,datetime,timedelta还有relativedelta。他们允许你计算日期值，在演示和测试数据时经常使用，所以日期的使用非常接近模块的安装日期。例如，设置一个昨天的时间，我们这样使用：
```xml
<field name="date_deadline" eval="(datetime.now() + timedelta(-1)).strftime('%Y-%m-%d')" />
```
在赋值内容中可以使用ref()函数，用来做扩展标识符和数据库ID的转换。这也可以用来设置相关联的字段
```xml
<field name="user_id" eval="ref('base.group_user')" />
```


##### Setting values for relation fields
我们来看看怎样设置一个多对一的关系字段，例如user_id，使用eval属性和ref()函数，但是还有一种更简单的方法。
```xml
<field name="user_id" ref="base.user_demo" />
```

对于一对多和多对多字段，一个关联IDs的列表可以使用，所以需要一个不同的语法。Odoo提供了一种特殊的语法来写这种类型的字段。
下面这个例子来自官方的车辆管理APP，代替了tag_ids相关的记录。

```xml
<field name="tag_ids"
eval="[(6,0,
	[ref('vehicle_tag_leasing'),
	ref('fleet.vehicle_tag_compact'),
	ref('fleet.vehicle_tag_senior')]
)]" />
```
为了写一个对多的字段，我们使用了一个三元列表。每一元是一个写命令，依据code做了不同的事情
```xml
	(0,_ ,{'field': value}) creates a new record and links it to this one
	(1,id,{'field': value}) updates the values on an already linked record
	(2,id,_)                unlinks and deletes a related record
	(3,id,_)                unlinks but does not delete a related record
	(4,id,_)                links an already existing record
	(5,_,_)                 unlinks but does not delete all the linked records
	(6,_,[ids])             replaces the list of linked records with the provided list
```
The underscore symbol used in the preceding list represents irrelevant values, usually filled with 0 or False.

#### Shortcuts for frequently used models
在前面第二章中，我们会还用了&lt;act_window&gt;和&lt;menuitem&gt;。这只是&lt;record&gt;的一种简写方式，也可以使用&lt;record&gt;来写。
下面是一些简写对应的模型
```xml
<act_window> is the window action model, ir.actions.act_window, 实际是ir_act_window
<menuitem> is the menu items model, ir.ui.menu
<report> is the report action model, ir.actions.report.xml, 实际是ir_act_report_xml
<template> is for QWeb templates stored in the model ir.ui.view
<url> is the URL action model, ir.actions.act_url, 实际是ir_act_url
```

#### Other actions in XML data files
到目前为止，我们只看到了使用XML文件添加和更新数据。特别地，也可以删除数据，执行任意的模型方法，触发工作流事件。
##### Deleting records
&lt;delete&gt;可以删除记录，可以通过ID或者一个search domain来定位记录。
```xml
使用ID
<delete model="ir.rule" id="todo_app.todo_task_user_rule" />
使用 search domain
<delete model="ir.rule" search=" [('id','=',ref('todo_app.todo_task_user_rule'))]" />
```
经过实际测试，可以这样使用
```xml
<?xml version="1.0"?>
<odoo>
    <data noupdate="0">
        <delete model="todo.task" id="todo_user.todo_task_f" />
    </data>
</odoo>
```

##### Triggering functions and workflows
&lt;function&gt;可以执行方法，例如
```xml
<function
	model="crm.lead"
	name="action_set_lost"
	eval="[ref('crm_case_7'), ref('crm_case_9')
			, ref('crm_case_11'), ref('crm_case_12')]
			, {'install_mode': True}" 
/>
```
这段代码执行`crm.lead`模型里的`action_set_lost`方法，使用eval属性传递两个参数。第一个是作用的ID，下一个是所用的内容。
另一个作用是触发工作流，属性是&lt;workflows&gt;。举个例子，工作流可以触发销售单的状态或者转化成一个发票。`sale`app不再使用工作流，但是这个例子在演示数据中仍然可以被发现：
```xml
<workflow model="sale.order"
	ref="sale_order_4"
	action="order_confirm" />
```
The `model` attribute is self-explanatory by now, and `ref` identifies the workflow instance we are acting upon. The `action` is the workflow signal sent to this workflow instance.

### Summary
You have learned all the essentials about data serialization and gained a better understanding of the XML aspects we saw in the previous chapters. We also spent some time understanding external identifiers, a central concept of data handling in general and module configurations in particular.
XML data files were explained in detail. You learned about the several options available to set values on fields and also to perform actions, such as deleting records and calling model methods.
CSV files and the data import/export features were also explained. These are valuable tools for Odoo's initial setup or for mass editing of data.

In the next chapter, we will explore how to build Odoo models in detail and learn more about building their user interfaces.

## 5,Models – Structuring the Application Data
接下来的章节中，将详细讨论这些层次：模型，视图和事物逻辑。在这一章里，学习怎样设计数据结构来支撑一个应用，还有怎样表示结构之间的关系。

### Organizing application features into modules
Odoo的继承机制提供一种有效的扩展机制。允许你扩展第三方应用而不用直接更改他们。这种可组合性也形成了以模块为导向开发模式，这样大的app可以被分成小的特性，足够表示他们自己。

这同时在技术层面和用户经验层面有助于限制复杂性。从技术角度看，分割一个大的问题到小的部分使问题更容易解决，对于增加的特性的开发也更友好。从用户经验角度看，我们可以选择激活用户真正需要的特性，使用尽量简单的用户界面。所以我们将会改进我们的To-Do 应用通过附加模块，最后形成一个具有全部特性的应用。


#### Introducing the todo_ui module
接下来，我们将会改进用户界面，包括一个kanban视图。kanban视图是一个简单的以列组织条目的工作流，这些条目从左向右流动，直到完成。我们会组织我们的任务进入对应的列，依据不同的阶段：等待，准备，开始和完成。
我们开始添加数据结构以完成上面的任务。我们需要添加stages，最好可以支持tags，允许任务通过主题分类。在这一章，我们会聚焦于数据模型。用户界面在下一章和第九章讨论。

第一件事是我们的数据怎样组织好以便我们可以设计支持模型。我们已经有了整个条目：To-do Task。每个task在一个时间将会处于一个阶段，tasks会有一个或者多个tags。我们将会添加两个附加模型，还有相应的关系：
	每个task有一个stage，很多tasks会处于同一个stage
	每个task有多个tags，每个tags有多个tasks
这意味着tasks和stages有多对一的关系，和tags有多对多的关系。反过来说：stages和tasks有一对多的关系，tags和tasks有多对多的关系。

### Creating models
todo_ui/models/todo_model.py内容为
```python
# -*- coding: utf-8 -*-
from odoo import models, fields, api

class Tag(models.Model):
    _name = 'todo.task.tag'
    _description = 'To-do Tag'
    name = fields.Char('Name', size=40, translate=True)

class Stage(models.Model):
    _name = 'todo.task.stage'
    _description = 'To-do Stage'
    _order = 'sequence,name'
    name = fields.Char('Name', 40, translate=True)
    sequence = fields.Integer('Sequence')
```
注意倒数第二行的40会引起编译错误，改成size=40，错误消失



#### Model attributes
一些模型属性
* `_name` is the internal identifier for the Odoo model we are creating. Mandatory when creating a new model.
* `_description` is a user friendly title for the model's records, shown when the model is viewed in the user interface. Optional but recommended.
* `_order` sets the default order to use when the model's records are browsed, or shown in a list view. It is a text string to be used as the SQL `order by` clause, so it can be anything you could use there, although it has a smart behavior and supports translatable and many-to-one field names.
注意在view视图里面，也有一个排序的属性

```xml
<tree default_order="calc desc,name">
```
还有一些属性被用在高级一点的情况。
* `_rec_name` indicates the field to use as the record description when referenced from related fields, such as a many-to-one relationship. By default, it uses the name field, which is a commonly found field in models. But this attribute allows us to use any other field for that purpose.
* `_table` is the name of the database table supporting the model. Usually, it is left to be calculated automatically, and is the model name with the dots replaced by underscores. But it's possible to set to indicate a specific table name.

We can also have the \_inherit and \_inherits attributes, as explained in Chapter 3, Inheritance - Extending Existing Applications.

#### Models and Python classes
Odoo模型使用了一个中央注册机制，在旧的odoo版本中可以参考`pool`。这是一个在实例中保存到所有模型类引用的字典，也可以被模型名称引用。特别地，在一个模型的方法中可以使用`self.env['x']`来得到一个类的引用表示模型x。
这样你就了解了模型名称在通过注册机制访问时的重要性。通常对模型名称来说使用小写字母并通过点连接，例如todo.task.stage。通常我们应该使用单数形式todo.task来代替todo.tasks，只有一些例外是历史原因造成的，如res.users。
模型名称必须是全局唯一。因为这个，第一个单词应该能表现模块的主要功能。在我们的例子中是todo。其它例子是核心模块project,crm,sale
python类名位于python文件中声明的地方。类名只在文件中标识代码，正因为如此，类名也不用加上主应用的前缀，我们使用stage类名来实现todo.task.stage模型完全没有问题。在其它模块中使用相同的类名也不会产生冲突的风险。

对于类定义来说，两种不同的常规命名法可以被使用：`snake_case`和`CamelCase`。python标准是使用后一种。

说来说去，模型的名称要唯一，因为最后映射到数据库表上面。类名随意，不起什么作用。

#### Transient and Abstract models
在之前的模型里，类继承自`models.Model`。这种类型的模型会被数据库留存：建立数据表并存储记录直到明确地删除它们。
但是odoo还提供了两种模型：Transient和Abstract
* Transient models are based on the `models.TransientModel` class and are used for wizardstyle user interaction. Their data is still stored in the database, but it is expected to be temporary. A vacuum job periodically clears old data from these tables. For example, the Load a Language dialog window, found in the Settings \| Translations menu, uses a Transient model to store user selections and implement wizard logic.
* Abstract models are based on the `models.AbstractModel` class and have no data storage attached to them. They act as reusable feature sets to be mixed in with other models, using the Odoo inheritance capabilities. For example, mail.thread is an Abstract model, provided by the Discuss addon, used to add message and follower features to other models.


#### Inspecting existing models
在**Setting**菜单 **Technical|Database Structure|Models**菜单里面，可以查看模型列表。
这是一个很好的检查模型结构的工具，你可以看到从不同模块改变的结果。在这种情况下，你可以看到`In Apps`字段，这个模型todo.task的定义，来自于todo_app和todo_user模块
接下来我们可以看到：字段，访问权，和这个模型关联的视图的page

我们可以找到模型的扩展标识符，使用查看元数据菜单就能看到。扩展标识符是由ORM自动产生的，比如todo.task模型的扩展标识符就是model_todo_task.

> 上面模型的视图是可以编辑的，可以从这里修改模型，字段，视图。在固化到模块之前可以先在这里建一个原型。

### Creating fields
Odoo支持text strings,integers,floating point numbers,Booleans,dates,datetimes and image/binary data.
一些字段名是特别的，因为他们被ORM保留做特殊的用途，或者由于一些内建的特性使用了一些默认的字段名。

#### Basic field types
在 Stage类上添加字段
```python
class Stage(models.Model):
    _name = 'todo.task.stage'
    _description = 'To-do Stage'
    _order = 'sequence,name'
    #string fields:
    name = fields.Char('Name', size=40, translate=True)
    desc = fields.Text('Description')
    state = fields.Selection(
        [('draft','New'),('open','Started'),('done','Closed')],
        'State',
        # selection_add= When extending a Model, adds items to selection list
    )
    docs = fields.Html('Documentation')

    #Numeric fields:
    sequence = fields.Integer('Sequence')
    perc_complete = fields.Float('% Complete', digits=(3,2))

    #Date fields
    date_effective = fields.Date('Effective Date')
    date_changed = fields.Datetime('Last Changed')

    #Other fields:
    fold =  fields.Boolean('Folded?')
    image = fields.Binary('Image')

```
很多情况下，第一个参数是field title,对应于string字段参数，这个参数就是用户界面的列名称。这个字段是可选的，如果没有使用，默认会由name字段产生。

对于date 字段来说，有一个通用的规则是使用date作为前缀。例如，date_effectiv。相同的规则也使用于其它字段：amount_,price_,或者qty_

还有一些标准的位置参数适用于各种字段类型
* Char expects a second, optional, argument size, for the maximum text size. It's recommended to not use it unless there is business requirement that requires it, such as a social security number with a fixed length.
* Text differs from Char , in that, it can hold multiline text content, but expects the same arguments.
* Selection is a drop-down selection list. The first argument is the list of selectable options and the second is the string title. The selection item is a list of ('value', 'Title') tuples, for the value stored in the database and the corresponding user interface description. When extending through inheritance, the **selection_add** argument is available to append new items to an existing selection list.
* Html is stored as a text field, but has specific handling on the user interface, for HTML content presentation. For security reasons, they are sanitized by default, but this behavior can be overridden.
* Integer just expects a string argument for the field title. 
* Float has a second optional argument, an (x,y) tuple with the field's precision: x is the total number of digits; of those, y are decimal digits.
* Date and Datetime fields expect only the string text as a positional argument. For historical reasons, the ORM handles their values in a string format. Helper functions should be used to convert them to actual date objects. Also the datetime values are stored in the database in UTC time but presented in local time, using the user's time zone preferences. This is discussed in more detail in Chapter 6 , Views - Designing the User Interface .注意使用UTC存储，而在界面中按实际时间显示。参照[Linux系统环境配置](SetupLinuxEnvironment.md#关于时区的讨论)
* Boolean holds True or False values, as you might expect, and only have one positional argument for the string text.
* Binary stores file-like binary data, and also expects only the string argument. They can be handled by Python code using base64 encoded strings.


#### Common field attributes
当字段定义的时候，可以设置字段属性。像前面一节那样，依赖字段类型，一些属性可以不使用关键字而通过位置传递。更多的关键字参数可以参照[Python官方文档](https://docs.python.org/2/tutorial/controlflow.html#keyword-arguments)
所有属性都可以使用关键字参数，下面是一些通常使用的属性和相应的关键字：

* `string` is the field default label, to be used in the user interface. Except for selection and relational fields, it is the first positional argument, so most of the time it is not used as a keyword argument.除selection和relational字段，string是第一个位置参数。
* `default` sets a default value for the field. It can be a static value, such as a string, or a callable reference, either a named function or an anonymous function (a lambda expression).
* `size` applies only to Char fields, and can set a maximum size allowed. Current best practice is to not use it unless it's really needed.
* `translate` applies only to Char , Text , and Html fields, and makes the field contents translatable, holding different values for different languages.
* `help` provides the text for tooltips displayed to the users.
* `readonly=True` makes the field by default not editable on the user interface. This is not enforced at the API level; it is only a user interface setting.
* `required=True` makes the field by default mandatory in the user interface. This is enforced at the database level by adding a NOT NULL constraint on the column.
* `index=True` will create a database index on the field.
* `copy=False` has the field ignored when using the duplicate record feature, copy() ORM method. The non-relational fields are copyable by default.
* `groups` allows limiting the field's access and visibility to only some groups. It expects a comma separated list of XML IDs for security groups, such as
```xml
groups='base.group_user,base.group_system'
```
**这一条非常重要，现在开始试验**
经过实际测试，在model中的某字段做出groups的限制后，form和tree视图统一添加该字段做显示。实际结果是只有groups里面的组可以在两个视图中看到这个字段，其它组既看不到也不能导出数据，即使是administrator也看不到，符合要求！！
* `states` expects a dictionary mapping values for UI attributes depending on values of the state field. For example: states={'done':[('readonly',True)]} . Attributes that can be used are readonly, required , and invisible.

> 注意states字段属性相当于视图的attrs属性。在视图属性中也支持一个states属性，但是是不同的用法：接受一个逗号分隔符的状态列表来控制元素何时被显示。

* `deprecated=True` logs a warning whenever the field is being used.
* `oldname='field'` is used when a field is renamed in a newer version, enabling the data in the old field to be automatically copied into the new field.

#### Special field names
很少的字段名被用于ORM
* `id`是一个自增的序列，使用的是数据库的主键，会自动添加到每一个模型

下面字段也会在新模型中自动建立，除非`_log_access=False`模型属性被设置
* `create_uid` is for the user that created the record
* `create_date` is for the date and time when the record is created
* `write_uid` is for the last user to modify the record
* `write_date` is for the last date and time when the record was modified

上面这些信息也可以通过激活开发者模式在 **View Metadata** 菜单看到.

一些API的内建特性需要通过默认的字段名实现，我们要避免使用这些字段名，其中的一些甚至完全不能被其它目的使用。

* `name` is used by default as the display name for the record. Usually it is a Char, but can also be a Text or a Many2one field type. We can still set another field to be used for display name, using the \_rec_name model attribute.

* `Active` of type Boolean, allows inactivating records. Records with active==False will automatically be excluded from queries. To access them an ('active','=',False) condition must be added to the search domain, or 'active_test': False should be added to the current context.
这个特性应该也会用到，非激活状态的数据不能被直接看到，但是可以设置过滤条件找到并导出，虽然不能用来保密，但是对于历史记录可以起到隐藏显示的作用。当流程走到最后一步，点击完成按钮就可以把Active设置成False。比如生产计划，正在进行的生产计划会显示，完成的会隐藏，但是想查看的时候也能查看。在界面上组合readonly="1"，即使有编辑权限的人也不能直接编辑，必须流程走到最后点击完成，实现隐藏。

* `Sequence` of type Integer , if present in a list view, allows to manually define the order of the records. To work properly you should not forget to use it with the model's \_order attribute.在数据库记录层面来调整显示顺序，需要配合\_order属性使用,指定数字123，就会按数字顺序显示记录。在view层面可以使用&lt;tree default_order="calc desc,name"&gt;

* `State` of type Selection , represents basic states of the record's life cycle, and can be used by the state's field attribute to dynamically modify the view: some form fields can be made readonly, required, or invisible in specific record states.
用来表示记录生命循环的基本状态，可以被state字段属性修改视图：一些form视图字段可以被设置只读，必填或者不可见。
没怎么用过，需要仔细看看，也许会有新发现。

* `parent_id , parent_left , and parent_right` of type Integer , have special meaning for parent/child hierarchical relations. We will discuss them in detail in the next section.这个可能在BOM中用到。

到目前为止，我们就讨论了非联系字段。但是一个应用最好的部分是描述实体之间关系的部分。我们继续看一看。

### Relationships between models
继续分析这个应用：
	每个task有一个stage，这是一个`多对一`的关系，也就是外键。反过来说就是`一对多`的关系，意味着处于一个stage会有很多task
	每个task有多个Tags，这是一个`多对多`的关系。反过来说也是`多对多`的关系，因为每个Tag有多个task
在模型文件中加入这一段，运行后查看数据库表会多一个stage_id列。还会多一个todo_task_todo_task_tag_rel表。
```python
class TodoTask(models.Model):
    _inherit = 'todo.task'
    stage_id = fields.Many2one('todo.task.stage', 'Stage')
    tag_ids = fields.Many2many('todo.task.tag', string='Tags')
```
对于关联字段的命名规则是添加\_id和\_ids，分别对应`对一`和`对多`关系

#### Many-to-one relationships
The `Many2one` relationship 接受两个位置参数：关联模型（使用comodel关键字）和列名（string关键字），它建立了一个对关联表的外键。
下面是一些附加的命名参数

* `ondelete` defines what happens when the related record is deleted. Its default is `set null`, meaning that an empty value is set when the related record is deleted. Other possible values are `restrict`, raising an error preventing the deletion, and `cascade` also deleting this record.
* `context` is a dictionary of data, meaningful for the web client views, to carry information when navigating through the relationship. For example, to set default vales. It will be better explained in the Chapter 6 , Views - Designing the User Interface.数据字典，对视图有意义，设置默认值，在第六章解释。
* `domain` is a domain expression, a list of tuples, used filter the records available for the relation field.对相关字段进行过滤
* `auto_join=True` allows the ORM to use SQL joins when doing searches using this relationship. If used, the access security rules will be bypassed, and the user could have access to related records the security rules wouldn't allow, but the SQL queries will be more efficient and run faster.当使用这个关联做查找时，允许ORM使用SQL的joins。如果使用这个关键字，则访问安全规则将会被忽略。用户将会访问之前规则不允许访问的关联记录，但是SQL的查询将会执行的更快。

#### Many-to-many relationships

The `Many2many` minimal signature accepts one argument for the related model, and it is recommended to also provide the string argument with the field title.

在数据库层面，并不添加任何列到已经存在的表，而是生成一个只有两个ID列的表，使用外键分别关联到原表上。新表名和列名自动产生，新表名是两个表连接到一起再加一个_rel属性。

在一些情况下，我们可能重写这些自动产生的名字：
* 一种情况是关联模型有一个长名字，导致新表名过长，超过了PostgreSQL63个字符的限制。在这种情况下我们需要手动指定新表名来适应数据库的长度限制。
* 另一种情况是在相同的模型中，需要第二个多对多关系。在这种情况下我们需要手动提供一个关联表名。所以不能和之前已经存在的第一个关系使用的表名产生冲突。

两种方法手动指定：位置参数或者关键字参数

位置参数
```python
tag_ids = fields.Many2many(
	'todo.task.tag',      # related= (model name)
	'todo_task_tag_rel',  # relation= (table name)
	'task_id',            # column1= ("this" field)
	'tag_id',             # column2= ("other" field)
	string='Tags')
```
> 我们也可以只设置表名，让字段名自动产生。

关键字参数
```python
# Task <-> Tag relation (keyword args):
tag_ids = fields.Many2many(
	comodel_name='todo.task.tag', # related model
	relation='todo_task_tag_rel',# relation table name
	column1='task_id', # field for "this" record
	column2='tag_id', # field for "other" record
	string='Tags')
```
就像多对一字段，多对多字段也支持domain和context属性

> 在ORM的设计中，有一个限制：关于抽象类，当你生成了关联表的名字和列名，他们不能被干净地继承。所以在抽象模型中不能使用这种关联。？？

多对多关系的反转也是多对多关系，如果我们在Tags模型中添加多对多关系，则Odoo也会推断是Task模型关系的反转。我们可以这样实现。
```python
# Many2many inverse relationship
task_ids = fields.Many2many(
	'todo.task',  #related model
	string='Tasks')
```

#### One-to-many inverse relationships
`一对多`这种关系虽然不在数据库中产生任何改变，但是可以让我们很容易的从`一`端查看对应的`多`条记录。一个典型的应用是一个文档标题对应多行文字。

在我们的例子里，是一个Stage会有多个Task，添加如下代码
```python
task_ids = fields.One2many(
	'todo.task', # related model, keyword arguments:comodel_name=
	'stage_id',  # field for "this" on related model,keyword arguments:inverse_name=
	'Tasks in this stage' #title string
	)
```

#### Hierarchic relationships
父子树的关系可以对同一个模型使用一个`多对一`关系，所以每个记录可以参照它的父亲。反过来，One2many可以让父亲很容易追踪到儿子。

Odoo改进了对这种分层数据结构的支持。通过树的兄弟进行更快的浏览，在domain表达式里面通过使用`child_of`操作符可以更容易的找到。

为了使用这个特性，我们需要设置`_parent_store`标志属性，添加模型帮助字段`parent_left 和 parent_right`。注意这个附加的操作在存储和执行上都有时间花费，所以适用于那些读多于写的情况，例如一个分类树。

在python文件添加如下代码
```python
    # Hierarchic relationships:
    _parent_store = True
    _parent_name = 'parent_id'  # the default
    parent_id = fields.Many2one(
        'todo.task.tag',
        'Parent Tag',
        ondelete='restrict')
    parent_left = fields.Integer('Parent Left', index=True)
    parent_right = fields.Integer('Parent Right', index=True)
```
执行后数据表会多出三列:parent_id,parent_left,parent_right。parent_id字段参照父记录，\_parent_store 属性添加了分层搜索支持。parent_left,parent_right也要一并增加
parent_id字段参照到父记录，我们也可以用\_parent_name属性指定

同样，在记录的直接孩子中可以非常方便的添加一个字段child_ids
```python
child_ids = fields.One2many(
        'todo.task.tag',
        'parent_id',
        'Child Tags')
```
我的理解是模型中的每条记录即是父又是子，这种父子关系是固定的，就像图书分类，每一类固定父类和子类，不会出现一个孩子既属于这个父类，又属于那个父类。但是在BOM中一个零件会出现在各个分支中。


#### Reference fields using dynamic relationships
通常的关联字段都是固定关联某个模型，参照字段类型没有这个限制并支持动态关系，所以同一个字段可以参照多个模型。例如我们可以使用一个refer_to字段既关联User又关联Partner:
```python
class TodoTask(models.Model):
	refers_to = fields.Reference(
	[('res.users', 'User'), ('res.partner', 'Partner')],
	'Refers to')
```
经过实际测试，在数据库的模型'todo.task'只多了一个字段，res.users和res.partner不知道存储在何处了

在视图面，会出现两个选择栏，先选择是res.users，还是res.partner,然后根据选择调出第二个选择栏。就像行政区划分，选择了省出现市，选择市出现县。

这个字段的意义可以这样利用，如果使用零件库和原料库，则可以同时关联这两个这两个仓库，然后就可以把零件和原料放在不同的模型中。兼容目前程序，避免混乱。

还可以有另外一种实现方式：在数据库结构菜单里面有“可参照模型”菜单，可以配置参照字段使用的模型。当建立一个这样的字段，我们就可以使用这里的任何模型了。我们是通过odoo.addons.res.res_request模块（实际是数据库表res_request_link）里的referenceable_models()函数。

像下面这样，使用参照模型菜单配置一个改进版本的`Refers to`字段
```python
from odoo.addons.base.res.res_request import referenceable_models
# class TodoTask(models.Model):
	refers_to = fields.Reference(
	referenceable_models,
	'Refers to')
```
这里的灵活性在于，我们在“可参照模型”菜单中设置好需要参照的模型，然后这里使用referenceable_models，不必把所有列表写死，以后还可以继续添加。

> 注意odoo9使用的api有些不同


### Computed fields
字段值可以通过函数计算出来，而不是简单的读取数据库的存储值。计算字段的声明和普通字段是一样的，但是附加了`compute`参数，来定义用于计算的函数。
在大多数情况下，计算字段用来写一些事物逻辑，所以我们将在Chapter 7, ORM Application Logic - Supporting Business Processes.里做更详细的讨论。我们在这里的讨论将保持事物逻辑的简单化。

我们以Stages模型里的fold字段为例演示。
```python
    stage_id = fields.Many2one(comodel='todo.task.stage', string='Stage1')

    # Calculated fields:
    stage_fold = fields.Boolean(
        string='Stage Folded?',
        compute='_compute_stage_fold',
        search='_search_stage_fold',
        inverse='_write_stage_fold',
        #store=False,  # the default
    )

    @api.depends('stage_id.fold')
    def _compute_stage_fold(self):
        for record in self:
            record.stage_fold = record.stage_id.fold
```
这个例子取`多对一`中`一`中一条记录的一个属性，给`多`中一个字段赋值。与我们使用assemble中取part的属性一样。

\_compute_stage_fold 函数名作为字符串传递，但也允许作为可以调用的引用传递（函数定义符没有引号）。在这种情况下我们
确保函数定义在该文件中使用的字段之前。

当计算依赖其他字段时，`@api.depends` 装饰符就是必要的了。装饰器让服务器知道何时重新计算存储或者缓存数据。一个或者更多字段名称被作为参数接收，而点表达式被用来分割接下来的字段关系。

计算函数期待指定一个值给字段或者字段用来计算。如果不是这样，将会错误。因为`self`是一个记录对象，我们这里的计算只是使用stage_id.fold简单的得到 **Folded?**字段的值。结果是取出指定值，写入计算字段：stage_fold。

对这个模块我们的视图还不能工作，但是你可以马上在task的form上做一个编辑以确认计算字段是否工作正常：使用开发者模式里的编辑视图选项，直接在XML form里面添加字段。不用担心：这个改变在下一次升级的时候将会被清除。
经过测试这确实是一个临时的方法，因为表示视图的XML文件没有被修改，所以下一次升级还会以XML文件为准。

在这个计算字段的测试中，升级模块时，pycharm会出现报错KeyError: 'compute_fold'，然后继续运行。也没找到更好的解决方法，重启电脑也没有解决。怀疑：数据库在不停的升级过程中弄坏了，换了一个全新的数据库就没有问题。

#### Searching and writing on computed fields
计算字段只能被读取，不能被搜索和写入，为了打开这个特性，我们需要实现特殊的函数。我们使用`search`和`inverse`函数实现搜索和写入逻辑。
```python
    def _search_stage_fold(self, operator, value):
        return [('stage_id.fold', operator, value)]

    def _write_stage_fold(self):
        self.stage_id.fold = self.stage_fold
```
当一个关于这个字段的(field, operator, value)条件在一个搜索domain表达式中被建立，`search`函数被调用。它接受`operateor`和`value`参数，翻译原始的搜索元素到一个替代的domain搜索表达式。

`inverse`函数执行运算逻辑的反向动作，发现数值并写入原始字段，如stage_id.fold


#### Storing computed fields
使用`store = True`计算字段的值也可以被存储在数据库，当依赖改变是，字段将会被重新计算。由于值被存储了，就可以像普通字段一样搜索，不用再写函数。

#### Related fields
使用关联字段可以和上面的计算字段赋值起到同样的效果。关联字段直接在一个模型上，字段属于一个相关的模型，访问使用一个点表达式链。这使得点表达式不能使用的情况下非常有用，比如UI form视图。

To-do Tasks被可改变Stages组织。我们将会使State值直接在Task模型上，所以在下一章将会使用一些客户端逻辑

我们在task模型上添加stage_state字段，并使用related属性。

这次我们
```python
  # Related fields:
    state = fields.Selection(
        related='stage_id.state',
        string='Stage State',
        store=True,  # optional
    )
```
在这种情况下，关联字段只是非常方便的实现了search和inverse的计算字段。就是不用像计算字段那样写函数了。

### Model Constraints
为了保证数据的完整性，模型支持两种类型的约束：SQL和Python

SQL约束直接添加到数据库表里面，由PostgreSQL保证，使用`_sql_constraints`属性，接着一个列表：约束名；约束SQL；错误提示

通常是添加唯一性约束给模型，假设我们不想两个激活的task的有相同的名称
```python
# class TodoTask(models.Model):
_sql_constraints = [
    ('todo_task_name_uniq',
    'UNIQUE (name, active)',
    'Task title must be unique!')]

```

Python约束可以使用任意的代码来检查条件。检查函数使用装饰器`@api.constraints`，后面接需要被检查的字段列表。当其中的任何字段改变时会触发正确性检查，如果条件为假则会引发异常。

例如检查字符名需要至少五个字符长度
```python
from odoo.exceptions import ValidationError

    @api.constrains('name')
    def _check_name_size(self):
        for todo in self:
            if len(todo.name) < 5:
                raise ValidationError('Title must have 5 chars!')

```
开始不好使，换了一个新数据库就好使了。


### Summary

We went through a detailed explanation of models and fields, using them to extend the To-Do app with Tags and Stages on tasks. You learned how to define relationships between models, including hierarchical parent/child relationships. Finally, we saw simple examples of computed fields and constraints using Python code.

In the next chapter, we will work on the user interface for these backend model features, making them available in the views used to interact with the application.


## 6,Views - Designing the User Interface
使用上一章的模型层，这一章建立视图层

### Defining the user interface with XML files
不要忘了在\__manifest__.py里声明XML文件

> 注意data文件的读取顺序，因为当前文件只能引用已经存在的XML ID

我们还要建立 views/todo_view.xml 和 views/todo_menu.xml

我们将在第三章的基础上继续添加menu和相应的windows actions

#### Menu items
菜单存储在ir.ui.menu里面，也可以在技术界面查看

todo_app建立了顶级菜单来打开task，现在我们让其仍然打开Task，建立二级菜单Tasks和Configuration，将其parent属性指向顶级菜单。

二级菜单Tasks还是打开todo_app建立的action：todo_app.action_todo_task
二级菜单Configuration使用了`groups`属性，只给admin用户使用，其它用户登录看不到

二级菜单Configuration添加三级菜单Stages，打开stages

注意，这些菜单的Action还没有定义，还不能运行升级选项，在下一节添加Action

`views/todo_menu.xml`添加如下代码

```xml
<!-- Menu items -->
<!-- Modify top menu item -->
<menuitem id="todo_app.menu_todo_task" name="To-Do" />

<!-- App menu items -->
<menuitem id="menu_todo_task_view"
  name="Tasks"
  parent="todo_app.menu_todo_task"
  sequence="10"
  action="todo_app.action_todo_task" />

<menuitem id="menu_todo_config"
  name="Configuration"
  parent="todo_app.menu_todo_task"
  sequence="100"
  groups="base.group_system" />

<menuitem id="menu_todo_task_stage"
  name="Stages"
  parent="menu_todo_config"
  sequence="10"
  action="action_todo_stage" />

```
这里我们直接使用了&lt;menuitem&gt;，代替&lt;record model="ir.ui.menu"&gt;




#### Window actions
一般使用菜单或者按钮调用window action来给GUI客户端传递指令。它来告诉GUI使用什么模型和视图。这些action可以使用domain关键字来只显示记录集的子集，也可以使用context属性来设置默认值和filter

将下面的action添加到`views/todo_menu.xml`,注意要在刚才添加菜单的前面

```xml
<!-- Actions for the menu items -->
<act_window id="action_todo_stage"
	name="To-Do Task Stages"
	res_model="todo.task.stage"
	view_mode="tree,form"
	target="current"
	context="{'default_state': 'open'}"
	domain="[]"
	limit="80"
/>
<act_window id="todo_app.action_todo_task"
	name="To-Do Tasks"
	res_model="todo.task"
	view_mode="tree,form,calendar,graph,pivot"
	target="current"
	context="{'search_default_filter_my_tasks': True}"
/>
<!-- Add option to the "More" button -->
<act_window id="action_todo_task_stage"
	name="To-Do Task Stages"
	res_model="todo.task.stage"
	src_model="todo.task"
	multi="False"
/>

```
action存储在ir_act_window数据库表里，可以使用简写&lt;act_window&gt;定义。

上面代码中的第一个action打开Task Stages，包括了window actions的大多数相关的属性

* `name` is the title that will be displayed on the views opened through this action.
* `res_model` is the identifier of the target model.
* `view_mode` is the view type available and their order. The first is the one opened by default.
* `target`, if set to `new`, will open the view in a pop-up dialog window. By default it is`current`, opening the view inline, in the main content area.
* `context` sets context information on the target views, which can set default values or activate filters, among other things. We will see it in more details in a moment.
* `domain` is a domain expression forcing a filter for the records that will be browseable in the opened views.
* `limit` is the number of records for each page, in the list view.

第二个action替换了原始的todo_app中的action，添加了马上要讲解的calendar和graph视图。当更新完毕，在右上角将会看到新的按钮，但是在添加代码之前还不能工作。

第三个action,还没有使用。它向我们显示了怎样向More菜单添加选项，在list和form视图的右上部分生效，为了做到这样的效果，我们使用两个特别的属性

* `src_model` indicates on what model this action should be made available.
* `multi`, when set to True , makes it available in the list view so that it can applied to a multiple selection of records. The default value is False , as in our example, it will make the option available only in the form view, and so can only be applied to one record at a time.

没有调用出来

### Context and domain
我们已经使用context和domain好几次了，而且模型中的关联字段也用它们作为属性。

#### Context data
**context**是一个字典承载session data，这种数据可以用在客户端的用户界面和服务器端的ORM和事物逻辑

客户端：从一个视图传递信息到另一个视图，比如在点击一个link或者按钮后在前面视图激活的记录ID，或者在接下来的视图中提供一个默认的值

服务器端：一些记录集的字段值依赖context提供的本地设定。特别是`lang`key影响翻译字段的值。Context也能给服务器端代码提供信号。例如，`active_test`key当设定到False时改变ORM的search()方法，以使它不能过滤出非激活字段

一个从web client而来的初始的context看起来是这样：
{'lang': 'en_US', 'tz': 'Europe/Brussels', 'uid': 1}
你可以看到语言，时区和当前用户

当从前一个视图的link或者button打开一个form视图，一个active_id就添加到context中，在原来的视图中这个ID是我们正在看的记录。在一种list视图特别的情况下，我们有`active_ids`context key 包含一个在之前视图选择的很多ID的列表。

在客户端，context可以被用于设置默认值，或者激活filter，这两种情况使用关键字`default_`或者`default_search_`。例如：
把当前的user作为user_id字段的默认值，我们会使用

```xml
{'default_user_id': uid}
```
在采购录入收货单时，采购者直接用这个default_user_id指定，然后readonly=1

想默认使用`filter_my_tasks`我们使用

```xml
{'default_search_filter_my_tasks': 1}
```

#### Domain expressions
域用来过滤数据记录，使用特别的Odoo ORM能解析的语法，解析后能产生SQL WHERE语句，然后查询数据库

格式是这样的('field_name', 'operator',value)，例如：[('is_done','=',False)]
在act_window里面使用domain后，会直接从数据库拿到符合条件的数据，不符合条件的数据不会被显示和导出。

下面解释一下表达式的元素
* `field name` is the field being filtered, and can use dot-notation for fields in related models
   domain="[('stage_id.state','=','done')]"
   
* `value` 被当作一个python表达式计算，可以是字面值：数字，布尔，字符串，列表，也可以使用evaluation context里的字段和标识符。下面是实际使用的两种evaluation contexts for domains
	用在客户端，例如windes actons或者field属性，raw field values用来呈现有效的当前视图，但是不能使用dot-notation
	用在服务器端，例如security record rules和server python code,dot-notation 可以在字段中被使用，因为当前record is a object

* `operator`
The usual comparison operators are < , > , <= , >=, =, !=

 * `=like` matches against a pattern, where the underscore symbol, _ , matches any single character, and the percentage symbol, %, matches any sequence of characters.

 * `like` matches against a '%value%' pattern. The 'ilike' is similar but case insensitive.

 * The `not like` and `not ilike` operators are also available.

 * `child of` finds the children values in a hierarchical relation, for the models configured to support them.

 * `in` and `not in` are used to check for inclusion in a given list, so the value should be a list of values. When used on a "to-many" relation field the in operator behaves like a contains operator.

当domain expression 是一个列表时，包含几个条件时，默认使用AND连接。如果显式的使用连接符，&表示AND，\|表示OR，连接符会作用在接下来的两个表达式。！表示NOT，会作用在接下来的一个操作符。所以应该这样使用['!',('is_done','=',True)]

next item 也可以是一个像上面的三元表达式，完成条件嵌套

在服务器端记录规则里面，我们可以发现像这样的domain expressions

```xml
['|', ('message_follower_ids', 'in',
[user.partner_id.id]),
'|', ('user_id', '=', user.id),
('user_id', '=', False)
]
```
This domain filters all the records where the current user is in the follower list, is the responsible user, or does not have a responsible user set.



### The form views
我们接下来会设计business document views和怎样使用elements，widgets。继续以继承和扩展todo_app视图为例。但是为了清楚起见，我们会建立一个完全的新视图替代原始视图。


#### Dealing with several views of the same type
一个模型可以有同一类型的多个视图。通过XML ID，Window action可以指定使用哪个视图。所以我们可以有两个不同的menu打开同一个模型的不同视图。通过添加一个`view_id`属性到window action，指定要使用视图的XML ID。例如，我们继续在`todo_app.action_todo_task`里面添加`view_id="view_form_todo_task_ui`。我们在todo_app里面修改后并没有看到效果，可能是todo_ui后加载覆盖了前面的修改。后来在todo_ui的action里加入view_id="todo_app.view_form_todo_task_second"修改成功

如果不指定视图ID，则会使用较低priority的视图。priority的默认值是16，新视图设置为15就会起作用。


#### Business document views
商业应用通常是一些记录的系统：库房里的产品，财务部的单据等等。很多记录数据可以用一张纸质文档表示。为了达到更好的用户体验，form视图要模仿这些文档。例如，在我们的app中，我们可以认为To-Do Task就是一张用来填写的简单的纸质文件。下面提供一个form view的骨架

```xml
   <record id="view_form_todo_task_ui" model="ir.ui.view">
      <field name="model">todo.task</field>
      <field name="priority">15</field>
      <field name="arch" type="xml">

        <form>
          <header>
          </header>

          <sheet>
            <!-- Title -->

            <!-- Smart buttons -->

            <!-- Details -->
          </sheet>
        </form>
      </field>
    </record>
```
如果不指定view的name，就自动产生。为了简单起见，我们使用省略的name

我们看到business document views使用了单个主要区域：header status bar,sheet for main content,history and communication(chatter)

history 和 communication section,使用了social network widgets(由mail addon module提供支撑)。为了使用这个功能，我们的模型应该继承mail.thread的多重继承模型，可以参照Chapter 3 , Inheritance - Extending Existing Applications


##### The header
顶部的header通常标识生命周期或者步骤，步骤的执行是通过action buttons来实现的。这些action buttons是普通的form buttons.重要的next steps可以被高亮，使用class="oe_highlight"

文档的生命周期使用`statusbar`widget,具体是通过一个表示当前文档正处于的生命周期的字段表示。通常是一个State selection字段或者Stage many-to-one字段。在几个Odoo 核心模块中都能发现这两个字段。

Stage字段是一个多对一的字段，使用一个支持的模型来设置流程的步骤。因为这样用户可以动态配置来满足特定的商业流程，并且特别地支持kanban视图。

state是一个选择列表，表示一个少量，稳定，流程的步骤，例如：New，In Progress和Done。由于它是静态的所以不能被用户配置，但非常容易被用户使用。The view 字段甚至有一个特别的支持：依赖于文档的状态，state 属性允许一个字段对用户是否有效

从时间上看，stages引入的比states晚。两者同时共存，但是Odoo核心有使用stages代替states的趋势。但是参照之前的说明，states仍然提供了一些stages没有的特性。

仍然可以结合二者的优点，mapping stages into states。这就是我们在之前的章节所作的。在Stages模型里添加一个state字段，然后在To-do task文档中通过一个计算字段使其生效，使用state字段的属性。

接下来我们继续扩展views/todo_view.xml文件中的header，添加一个 status bar

```xml
          <header>
              <field name="state" invisible="True"/>
              <button name="do_toggle_done" type="object"
                      attrs="{'invisible':[('state','in',['draft'])]}"
                      string="Toggle Done"
                      class="oe_highlight"/>
              <field name="stage_id"
                     widget="statusbar"
                     clickable="True"
                     options="{'fold_field': 'fold'}"/>
          </header>
```
这里我们使用了state字段作为hidden字段。我们需要强制客户端包括这个字段从server取得数据。否则就不能用于表达式里面。
> 这里有一个非常重要的原则：任何想要使用的字段，在domain或者attrs表达式中，必须被读入到view中，所以在你需要的时候让字段隐藏，用户就看不见了。

下一行，一个button添加到status bar，让用户开关 task Done flag。这个button根据当前文档所处的生命周期的阶段决定是否出现在状态栏里。当文档处于draft状态时，我们使用`attrs`属性隐藏按钮。这个条件判断使用了state字段，前面添加的隐藏字段。

如果我们有一个state选择字段，我们可以不使用states属性。在这种情况下，相同的实现可以通过使用states="open,done"。它更加简洁，但是没有attrs属性灵活。

这些可视的特性也可以用于其它的视图元素，例如字段。我们将会在这一章的后面详细的解释。

`clickable`属性允许用户通过点击状态栏来改变文档的stage。我们通常打开这个功能，但是有些时候需要关闭，比如当我们要给workflow更多的控制的时候，我们需要用户仅通过有效的button来处理stages，这样在stages期间移动的时候可以执行验证。

当使用status bar widget处理stages，我们很少使用stages隐藏在一个更多的stage组中。对于这一点，stages模型必须有一个flag来配置隐藏项，通常命名为fold。status bar widget应该使用一个option属性，就像前面代码显示的那样，提供字段名给fold_field选项。

当使用一个state字段来使用status bar widget，使用statusbar_visible属性也可以实现一个相似的效果。这个属性用于列出应该看见的状态，极少的情况下隐藏例外的状态。这样使用
```xml
<field name="stage_id" widget="statusbar"
clickable="True"
statusbar_visible="draft,open" />
```

在这一节我们完成了header设计，button可以根据文档状态（stage）显示与否，使用这种特性我们可以灵活的实现控制

##### The sheet
sheet画布是form的主要区域，展示实际的数据元素。设计成纸质文档样子，通常可以看到在Odoo里记录是作为文档提供的。
通常，一个文档结构包括这些区域：
* A document title and subtitle at the top.
* A button box at the top-right corner.
* Other document header fields.
* A notebook for additional fields organized in tabs or pages. Document lines would also go
here, usually in the first notebook page.

让我们来看一看
##### Title and subtitle
在&lt;group&gt;外面的元素是无法自动拥有label来显示它们。&lt;label for"..."/&gt;元素用来表示title，通过一些额外的工作，给label显示更多的控制。
HTML和CSS元素可以使title更好看。一般来说title应该在&lt;div&gt;里使用oe_title类。
```xml
  <div class="oe_title">
       <label for="name" class="oe_edit_only"/>
       <h1><field name="name"/></h1>

       <h3>
         <span class="oe_read_only"> By</span>

         <label for="user_id" class="oe_edit_only"/>
         <field name="user_id" class="oe_inline" />
       </h3>
  </div>
```
这里我们使用了HTML元素，div, span, h1, and h3，&lt;label&gt;元素允许我们控制。for属性指定字段ID。另一个使用属性是string，给label提供一个特定的text。我们还使用了class="oe_edit_only"属性，以便于在编辑模式下是可见的。

在一些情况下，比如partner和product，在左上角落里有一个展示的图片。假设我们有一个my_image binary字段我们可以在div class="oe_title"前面使用
&lt;field name="my_image" widget="image" class="oe_avatar"/&gt;


##### Smart buttons area
右上的区域可以有一个不可见的box用来放置buttons。从8.0开始引入smart buttons,
带着一个静态指示器形状为正方形，可以通过点击被follow

在oe_title后面的DIV，我们添加
```xml
           <div name="buttons" class="oe_right oe_button_box">
                <button class="oe_stat_button"
                        type="action" icon="fa-tasks"
                        name="%(action_todo_task_button)d"
                        context="{'default_user_id': user_id}"
                        string=""
                        help="Other to-dos for this user" >
                    <field string="User To-dos" name="user_todo_count"
                           widget="statinfo"/>
                </button>
            </div>
```

装buttons的容器是div，有两个class：oe_button_box和oe_right，oe_right用来把它放置在form的右边。我们将会讨论buttons的细节在接下来的部分，所以我们会添加一个实际的buttons在这个box里。

##### Grouping content in a form
主要的内容用&lt;group&gt;来组织，它在区域中插入两列，一列值，一列label，所以往里添加字段的时候会从上到下摞起来显示。如果使用嵌套的&lt;group&gt;，就会显示四列，但是经实验单纯使用嵌套不会超过四列，我们添加如下代码：
```xml
           <group name="top_group">
              <group name="left_group">
                <field name="date_deadline" />
                <field name="effort_estimate" />
                <separator string="References" />
                <field name="refers_to" />
              </group>
              <group name="right_group">
                <field name="tag_ids" widget="many2many_tags"/>
              </group>
            </group>
```
给group起个名字是不错的做法，方便在随后引用它们或者由自己或者他人扩展view。string属性也是可以使用的，用来显示section title

在&lt;group&gt;里，&lt;newline&gt;元素将会强制新起一行，紧跟着的元素就会占据第一列

另外section title可以使用&lt;separator&gt;元素来显示。

> Deleloper 菜单 Toggle Form Layout Outline选项：在每个&lt;group&gt;部分画线，使我们更好的理解当前的布局。

我们还可以使用col和colspan属性来控制group元素的布局
* col属性可以控制&lt;group&gt;元素里面显示的列，偶数为佳
* 在group里面的元素，包括&lt;field&gt;元素，可以使用colspan属性设置一个占用几列位置的数字，默认是1。

##### Tabbed notebooks
组织内容的另外一个方法是使用notebook元素，包含多个标签，称之为页面。这个功能可以保持视野内有很少的数据，直到需要的时候再调出来。还有一个用途是以主题来组织大量的字段。
下面是示例代码
```xml
                <notebook>
                    <page string="Whiteboard" name="whiteboard">
                        <field name="docs"/>
                    </page>
                    <page>
                        <!-- Second page content -->
                    </page>
                </notebook>
```

### View semantic components
我们已经看到怎样使用结构组件，例如header，group和notebook等在一个form里组织内容。下面我们继续看一下semantic组件，字段和按钮，看看有什么功能。
#### Fields
View 字段有一些属性。大部分的值可以从模型的定义得到，但是将被view里相同的属性改写。 **也就是说相同的属性最后还是要看视图中的字段怎么定义**。这些属性都是通用的，不依赖于字段类型
* name: identifies the field database name.
* string: is the label text, to be used if we want to override the label text provided by the model definition.
* help: is a tooltip text shown when you hover the pointer over the field, and allows to override the help text provided by the model definition.
* placeholder: is a suggestion text to display inside the field.没有实验出来
* widget: allows us to override the default widget used for the field. We will explore the available widgets in a moment.
* options: is a JSON data structure with additional options for the widget and depends on what each widget supports.
* class: are the CSS classed to use for the field HTML rendering.
* nolabel="True" prevents the automatic field label from being presented. It only makes sense for the fields inside a &lt;group&gt; element and is often used along with a &lt;label for="..."&gt; element.
* invisible="True" makes the field not visible, but it's data is fetched from the server and is available on the form.
* readonly="True" makes the field non-editable on the form.可以指定数据在界面层能否被编辑，这个应该也很有用。例如：&lt;field name="active" readonly="1"/&gt;
* required="True" makes the field mandatory on the form.

Attributes specific to some field types are:
* password="True" is used for text fields. It is displayed as a password field, masking the characters typed in.
* filename is used for binary fields, and it is the name of the model field to be used to store the name of the uploaded file.
* mode is used for one-to-many fields. It specifies the view type to use to display the records. By default, it is tree, but can also be form, kanban, or graph.


##### Labels for fields
&lt;label&gt;元素可以被用于更好的显示一个字段标题。一个情况是只当form处于编辑模式的时候，用于显示标题。
&lt;label for="name" class="oe_edit_only" /&gt;
当使用了这条语句，如果字段在&lt;group&gt;里面，我们通常希望设置nolabel="True"

##### Relational fields
在相关字段，在用户的行为上我们可以有一些额外的控制。默认情况，用户可以从这些字段（也叫quick create）建立新的记录，然后打开相关记录。如果禁止这种行为，我们使用options属性：
options="{'no_open': True, 'no_create': True}"
在关联字段里，context和domain也非常有用。context可以对相关记录定义默认值，domain可以限制选择的记录数。一个通用的例子是在使用一个字段得到记录列表是依赖于当前记录的另一个字段。domain可以在模型定义，然后在视图中被改写。

##### Field widgets
每一个字段类型，在form中会使用合适的默认widget。但是还有一些widgets可以使用。
对于text字段
* email用于e-mail text一个可以使用电子邮件发送的地址
* url用于格式化text作为一个可以点击的URL
* html用于显示HTML内容，在编辑模式下，作为一个WYSIWYG编辑器来格式化内容，并不需要懂html语法

对于数字字段
* handle指定序列字段在list里的设计，显示了一个handle，允许放置lines到一个custom order
* float_time 格式化一个浮点字段，使用小时和分钟等时间量
* monetary 显示一个浮点字段作为货币量。它需要一个货币ID配合字段，但是另一个字段名可以使用下面提供 option="{'currency_field':'currency_id'}"
* progressbar 显示一个浮点数作为 进程百分比，可以用于表示一个完成度

对于关联字段和选择字段
* many2many_tags显示值作为按钮样子标题的list
* selection 使用selection字段widget，对many-to-one字段
* radio显示selection字段选项，使用radio 按钮
* kanban_state_selection 对kanban状态selection列表显示了一个信号灯。通常状态是灰色，完成后是绿色，其它状态显示为红色
* priority 显示了selection字段作为一个可点击的星状列表。selection选项通常是一个数字的

#### Buttons
button支持下列属性
* icon  is for icon image to use in the button to display; unlike smart buttons, the icons available for regular buttons are limited to the ones available in addons/web/static/src/img/icons .
* string  is the button text label, or the HTML alt text when an icon is used.
* type is the type of the action to perform. Possible values are:
 * workflow  is used to trigger a workflow engine signal;
 * object  is used for calling a Python method;
 * action  is used to run a window action.
* name  identifies the specific action to perform, according to the chosen type: either a workflow signal name, a model method name, or the database ID of window action to run. The %(xmlid)d formula can be used to translate the XML ID into the required Database ID.
* args  is used when the type is object , to pass additional parameters to the method.
* context  adds values to the context, that can have effects after the windows action is run, or in the Python code methods called.
* confirm  displays a confirmation message box, with the text assigned to this attribute.
* special="cancel"  is used on wizards, to cancel and close the wizard form.
#### Smart buttons
在设计了form结构的时候，我们在右上空间包含了一个smart buttons。让我们添加一个button

对于我们的app，我们实现一个显示所有者当前正在to-do的总数量，然后点击它就会导航到具体条目的list

首先我们需要添加相应的计算字段给models/todo_model.py
```python
    # Chapter 06 Smart Button statistic
    def compute_user_todo_count(self):
        for task in self:
            task.user_todo_count = task.search_count(
                [('user_id', '=', task.user_id.id)])

    user_todo_count = fields.Integer(
        'User To-Do Count',
        compute='compute_user_todo_count')
```
接下来，我们添加button，在oe_title div后面加入
```xml
               <button class="oe_stat_button"
                        type="action" icon="fa-tasks"
                        name="%(action_todo_task_button)d"
                        context="{'default_user_id': user_id}"
                        string=""
                        help="Other to-dos for this user" >
                    <field string="User To-dos" name="user_todo_count"
                           widget="statinfo"/>
                </button>
```
这个按钮显示了todo tasks相关负责人在整个task里所有任务的总数，是使用user_todo_count计算出来的。

下面这些属性我们用于添加smart buttons
* class="oe_stat_button" renders a rectangle instead of a regular button.
* icon sets the icon to use, chosen from the Font Awesome set. Available icons can be browsed at http://fontawesome.io.
* type and name are the button type and the name of the action to trigger. For smart buttons the type will usually be action, for a window action, and name will be the ID of the action to execute. It expects an actual database ID, so we have to use a formula to convert an XML ID into a database ID: "%(action-external-id)d" . This action should open a view with the related records.
* string adds label text to the button. We have not used it here because the contained field already provides a text for it.
* context should be used to set default values on the target view, to be used on new records created on the view after clicking through the button.
* help adds a help tooltip displayed when the mouse pointer is over the button.

button元素自己是一个container，拥有字段显示统计数字。这些普通的字段使用的widget是statinfo。这个字段应该是在模型中定义的计算字段。在button里面也可以使用静态文本，例如
```xml
<div>User's To-dos</div>
```
当点击按钮时，看到了一个list，只显示当前负责用户的task。这个行为是由action_todo_task_button实现的，所以就需要知道当前的responsible user,我们是使用button的context属性来存储这个值的。

我们必须把这个action放到XML文件的前面，以便后面的form使用它。
```xml
<act_window id="action_todo_task_button"
  name="To-Do Tasks"
  res_model="todo.task"
  view_mode="tree,form,calendar,graph,pivot"
  domain="[('user_id','=',default_user_id)]"
/>
```
注意我们在domain里使用 default_user_id context key。通过点击这个smart button进入的列表，当建立新task时，这个特别key将会设置默认值在user_id字段。

### Dynamic views
View 元素也支持一些动态属性，依赖字段值以允许视图动态改变行为和表现。我们有on change events，在一个form里，改变一个字段值可以使其它字段值改变。或者当一些条件满足的时候，使字段必填或者可见。

#### On change events
On change机制允许我们当一个指定的字段改变时，改变其它字段值。例如，当产品改变时，可以设置默认的价格。

在旧版本中，on change事件定义在view级别，但是从8.0开始，直接在模型层定义，就不需要在视图里使用特别的标记。实现机制的方法是，建立一个方法来计算，使用@api.onchange('field1','field2')来绑定字段。这些onchange方法的细节更多在Chapter 7 , ORM Application Logic - Supporting Business Processes

#### Dynamic attributes
为了马上响应用户输入，on change机制依赖于计算字段的重新计算。使用前面的例子，如果改变商品则价格字段改变，一个计算总价的字段也会使用新价格自动更新。动态属性：一些属性提供了一个容易的办法来控制特别用户界面元素的可见性。
* groups可以使用当前用户依赖的群组来决定元素的可见。多用户组可以使用逗号分隔符
* states可以依赖记录的State字段决定元素的可见。多State值可以使用逗号分隔符
* 还有一个方法是依赖客户端动态计算表达式来决定元素的可见。方法是使用attrs指定属性，使用一个字典映射可见属性的值给表达式的结果。例如，为了是除了draft状态的refers_to字段可见，我们使用

```xml
自用
<field name="refers_to" attrs="{'invisible':[('state','in',['draft'])]}" />
书中
<field name="refers_to" attrs="{'invisible':state=='draft'}" />
```
这个invisible属性对任何元素来说都是有效的，而不仅仅是字段。例如，我们也可以用在notebook pages和group元素上

attrs也可以给两个属性设置值：readonly和required。这些也对数据字段有意义，使他们不可编辑或者必填。这可以允许我们实现一些简单客户端逻辑。比如使一个字段根据State的状态来表现是否必填。

### List views
在这一点上，list view不需要介绍很多，但是我们仍然讨论一些能被使用的属性。这是一个我们使用list view的例子
```xml
    <!-- To-Do Task List view -->
    <record id="todo_app.view_tree_todo_task" model="ir.ui.view">
    <field name="model">todo.task</field>
      <field name="arch" type="xml">

        <tree decoration-muted="is_done"
          decoration-bf="state=='open'"
          delete="false">
          <field name="name"/>
          <field name="user_id"/>
          <field name="is_done"/>
          <field name="state" invisible="1"/>
        </tree>

      </field>
    </record>
```
行文本颜色和字体依赖python表达式计算的结果可以动态改变。用decoration–NAME属性带着一个建立在字段属性的表达式。Name部分可以使用bf或者it，对应粗体和斜体字，或者使用Bootstrap文本情景颜色：danger,info,muted,primary,success,或者warning。[参考文档](http://getbootstrap.com/css/#helper-classes-colors)

> color和fonts属性在8.0版本生效，到9.0取消。现在应该使用新的装饰属性。

还有就是注意前面提到过的，表达式用的字段值也要写进来，如果不想显示使用invisible="1"隐藏。

其他和tree视图相关的属性
* `default_order` allows to override the model's default sort order, and its value follows the same format as in order attribute used in model definitions.
* `create , delete` , and `edit` , if set to false (in lowercase) disables the corresponding action on the list view.
* `editable` makes records editable directly on the list view. Possible values are top and
bottom, the location where the new records will be added.

list视图可以包含字段和按钮，大部分form视图的属性在这里也有效。

在list视图里，数字字段可以显示列的总和。为了实现这个功能，我们要在字段里加入聚合属性，sum,avg,min或者max，然后指定一个总和值的标题，例如
&lt;field name="amount" sum="Total Amount" /&gt;

觉得直接在tree视图里这样用不太靠谱，在网上找了两个例子，认为在模型里的字段应该加入group_operator="sum",。后来经验证不需要修改模型里的字段，只要像上面一样直接在视图里数字字段后面加入sum,avg,min或者max就可以了。

### Search views
search 选项的有效值在&lt;search&gt;里定义。但我们在搜索框内输入的时候，我们可以选择自动搜索的字段。我们也可以在list视图里提供一个预定义的filter，或者预定义一个分组选项，这些都可以通过点击使用。下面是一个例子
```xml
    <record id="todo_app.view_filter_todo_task" model="ir.ui.view">
      <field name="model">todo.task</field>
      <field name="arch" type="xml">

        <search>
          <field name="name"/>
          <field name="user_id"/>

          <filter name="filter_not_done" string="Not Done"
                  domain="[('is_done','=',False)]"/>
          <filter name="filter_done" string="Done"
                  domain="[('is_done','!=',False)]"/>

          <separator/>
          <filter name="group_user" string="By User"
                  context="{'group_by': 'user_id'}"/>
        </search>

     </field>
    </record>
```
我们可以看到搜索使用两个字段：name和user_id。当用户在搜索框打字的时候，一个可用搜索字段的下拉列表就会出现。如果用户直接输入回车，第一个搜索字段就会使用。

接下来是两个filter。这两个filter可以独立使用，也可以同时点击做OR逻辑。如果filter之间使用了&lt;separator/&gt;，那选择两边的filter会使用AND逻辑。

第三个filter是通过context设置了一个组选项。这个高速视图分组使用哪个字段，这个例子中是user_id

字段元素还可以使用下列属性
* `name` identifies the field to use.
* `string` is a label text which is used instead of the default.
* `operator` is used to change the operator from the default one (= for numeric fields and ilike for the other field types).
* `filter_domain` sets a specific domain expression to use for the search, providing one flexible alternative to the operator attribute. The searched text string is referred in the expression as self. A trivial example is: filter_domain="[('name', 'ilike', self)]" .
* `groups` makes the search on the field available only for users belonging to some security Groups. Expects a comma separated list of XML IDs.
这里留一个疑问：如果供应商，客户，partner，user都在一个联系人里面出现，那么如何限制其他组不能看到供应商。

对于filter元素，这些属性是可用的
* `name` is an identifier to use by inheritance or to enable it through window actions. Not mandatory, but it is a good practice to always provide it.
* `string` is the label text to display for the filter. Required.
* `domain` is the domain expression to be added to the current domain.
* `context` is a context dictionary to add to the current context. Usually sets a group_id key with the name of the field to group records.
* `groups` makes the search on the field available only for a list of security Groups (XML IDs).

### Calendar views
就像名字所写，这种视图类型在日历上呈现记录，日历可以按月，星期，或者天显示。下面是个todotasks例子
```xml
    <record id="view_calendar_todo_task" model="ir.ui.view">
        <field name="model">todo.task</field>
        <field name="arch" type="xml">

            <calendar date_start="date_deadline"
                      color="user_id"
                      display="[name], Stage [stage_id]">
                <!-- Fields used for the display text -->
                <field name="name"/>
                <field name="stage_id"/>
            </calendar>

        </field>
    </record>

```
日历视图属性有
* `date_start` is the field for the start date. Mandatory.
* `date_end` is the field for the end date. Optional.
* `date_delay` is the field with the duration in days, that can be used instead of `date_end`.
* `all_day` provides the name of a Boolean field that is to be used to signal full day events. In these events, the duration is ignored.
* `color` is the field used to group color the calendar entries. Each distinct value in this field will be assigned a color, and all its entries will have the same color.
* `display` is the display text for each calendar entry. It can user record values using the field names between square brackets, such as [name]. These fields must be declared as child of the calendar element, an in the preceding example.就是说，想display，必须先声明。
* `mode` is the default display mode for the calendar, either day, week, or month.


### Graph and pivot views
Graph视图提供了数据的图形，是一个图表的form。当前的字段没有太适合图表展示的，我们会新加一个字段。

effort_estimate = fields.Integer('Effort Estimate')

把这个字段添加到form视图里，我们就能给这个字段添加值了。下面我们添加graph视图
```xml
    <record id="view_graph_todo_task" model="ir.ui.view">
        <field name="model">todo.task</field>
        <field name="arch" type="xml">

            <graph type="bar">
                <field name="stage_id"/>
                <field name="effort_estimate" type="measure"/>
            </graph>

        </field>
    </record>
```
图表视图元素有一个类型属性：bar，pie或者line。在bar这个例子里，附加的stacked="True"可以使其成为一个可堆放的图表，没试验出来。

这个数据也可以使用pivot表格展示，一种动态分析矩阵。从9.0开始引入pivot table，在8.0中也有效。但是在9.0中，拥有了自己的视图类型。同时还改进了UI特性和优化了获取pivot列表中的数据，下面是例子

```xml
    <record id="view_pivot_todo_task" model="ir.ui.view">
        <field name="model">todo.task</field>
        <field name="arch" type="xml">

            <pivot>
                <field name="stage_id" type="col"/>
                <field name="user_id"/>
                <field name="date_deadline" interval="week"/>
                <field name="effort_estimate" type="measure"/>
            </pivot>

        </field>
    </record>
```
图表和旋转视图应该包含字段元素，用来描述轴和使用的措施。大部分有效的属性已经在view类型中说过了：

* `name` identifies the field to use in the graph, just like in other views
* `type` is how the field will be used, as a `row` group (default), a `measure`, or as `col` (only for pivot tables, use for column groups)
* `interval` is meaningful for date fields, and is the time interval used to group time data by `day`, `week`, `month`, `quarter`, or `year`

默认情况下，聚合使用的是总和属性。也可以在python字段定义中使用group_operator属性，可选项为avg,max和min。

### Other view types
你还需要注意的是，我们还没有介绍三种视图；kanban，gantt和diagram

kanban将会在Chapter 9 , QWeb and Kanban Views.里面介绍

gantt在8.0里面有效，但是到9.0被放到企业版里面了

最后，diagram被用在很少的情况，附加模块很到会用到它们。只是你应该知道这两种视图的官方文档在哪里能找到：[文档链接](https://www.odoo.com/documentation/10.0/reference/views.html)

### Summary
Summary In this chapter, we learned more about Odoo views in order to build the user interface, covering the most important view types. In the next chapter, we will learn more about adding business logic to our applications.


## 7,ORM Application Logic – Supporting Business Processes
使用Odoo programming API，我们可以写下复杂的逻辑和向导，以便我们的app可以提供很好的用户交互体验。在这一章，我们将会看到在模型中怎样编程来完成复杂的事物逻辑，还会学习在事件和用户动作里怎么调用事物逻辑。

我们可以在事件中执行计算和校验，例如建立或者写一条记录，或者当一个按钮点击的时候执行一些逻辑。例如，我们给todo task完成的按钮，触发Is Done标志，清除所有标志为done的tasks

另外，我们可以使用向导和用户进行非常复杂的交互，在交互期间允许输入并且提供反馈。马上看一个例子

### Creating a wizard
假设我们的todo app用户经常需要对大量的tasks来设置deadlines和responsible。他们应该使用向导来完成任务。向导要让用户选择想要更新的tasks，然后选择deadline和responsible user来设置它们

向导是一种form视图，用来从用户那里得到输入信息，然后使用信息进行下一步处理。可以用作简单的任务，例如询问一小部分参数，然后返回一个报告。也可以用于复杂的数据处理，例如刚才描述的用例。下面是我们向导的例子

我们还是从新建一个模块todo_wizard开始。在todo_wizard/__manifest__.py里面添加如下代码：
```xml
{
    'name': 'To-do Tasks Management Assistant',
    'description': 'Mass edit your To-Do backlog.',
    'author': 'Daniel Reis',
    'depends': ['todo_user'],
    'data': ['views/todo_wizard_view.xml'],
}
在todo_wizard/__init__.py文件里只有一行
from . import models
```
接下来我们描述数据模型

#### The wizard model
向导对用户显示了一个form视图，通常是一个Windows对话框，里面有一些字段需要填充。这些信息一会儿向导逻辑会用到。
和前面一样，也是使用模型/视图架构，但是继承的模型从models.Model变成models.TransientModel。这种类型的模型也会在数据库里生成表，但是只会用到向导完成工作。一个定期的任务会从向导数据库表里面清除旧的数据。
在models/todo_wizard_model.py文件里定义我们需要和用户交互的字段：需要更新的task列表，负责人，和截止日期

首先在models/__init__.py里面添加 from .import todo_wizard_model 然后建立models/todo_wizard_model.py加入如下代码
```python
# -*- coding: utf-8 -*-
from odoo import models, fields, api

class TodoWizard(models.TransientModel):
    _name = 'todo.wizard'
    _description = 'To-do Mass Assignment'
    task_ids = fields.Many2many('todo.task', string='Tasks')
    new_deadline = fields.Date('Set Deadline')
    new_user_id = fields.Many2one('res.users', string='Set Responsible')
```
值得一提的是在transient模型里不能使用对普通models的one-to-many关系。原因是那将要求普通模型对transient模型有反转的many-to-one关系，但这是不允许的，因为这会引起普通模型记录和transient模型记录一起进行废料收集。

#### The wizard form
向导视图和普通的模型没什么区别，除了下面两个元素
* &lt;footer/&gt;部分可以用来放置action buttons
* 一个特别的type=“cancel”按钮来中断向导，而不用进行任何操作。

下面是views/todo_wizard_view.xml文件的内容
```xml
<odoo>

    <record id="view_form_todo_wizard" model="ir.ui.view">
        <field name="name">view_form_todo_wizard</field>
        <field name="model">todo.wizard</field>
        <field name="arch" type="xml">

            <form>
                <div class="oe_right">
                    <button type="object" name="do_count_tasks" string="Count"/>
                    <button type="object" name="do_populate_tasks" string="Get All"/>
                </div>

                <field name="task_ids">
                    <tree>
                        <field name="name"/>
                        <field name="user_id"/>
                        <field name="date_deadline"/>
                    </tree>
                </field>

                <group>
                    <field name="new_user_id"/>
                    <field name="new_deadline"/>
                </group>

                <footer>
                    <button type="object" name="do_mass_update"
                            string="Mass Update" class="oe_highlight"
                            attrs="{'invisible': [
                        ('new_deadline','=',False),
                        ('new_user_id','=',False)
                        ]}"/>
                    <button special="cancel" string="Cancel"/>
                </footer>
            </form>

        </field>
    </record>

    <!-- More button Action -->
    <act_window id="todo_app.action_todo_wizard"
                name="To-Do Tasks Wizard"
                src_model="todo.task"
                res_model="todo.wizard"
                view_mode="form"
                target="new"
    />

</odoo>

```
form和action添加完成后，我们并没有添加menu，而是使用了src_model属性，把菜单添加到todo.task的form视图的action视图里面了。

你还会注意到Mass Update按钮是隐藏的，除非你选择了负责人或者结束时间。



#### The wizard business logic
接下来，我们需要实现在form上button的动作。除了cancel按钮，我们有三个button要实现，现在我们看看Mass Update按钮。

button的name属性是do_mass_update，就是模型中的方法名，在models/todo_wizard_model.py文件里添加如下代码：
```python
from odoo import exceptions

import logging
_logger = logging.getLogger(__name__)

   @api.multi
    def do_mass_update(self):
        self.ensure_one()
        if not self.new_deadline and not self.new_user_id:
            raise exceptions.ValidationError('No data to update!')
        _logger.debug('Mass update on Todo Tasks %s' % self.task_ids)
        # Values to Write
        vals = {}
        if self.new_deadline:
            vals['date_deadline'] = self.new_deadline
        if self.new_user_id:
            vals['user_id'] = self.new_user_id
        # Mass write values on all selected tasks
        if vals:
            self.task_ids.write(vals)
        return True

```
我们的code应该一次处理一个向导实例，所以我们使用self.ensure_one()来保证。这里self代表在向导form里面浏览记录的数据。

方法开始于对用户是否给出新的结束日期或者负责人进行检验，如果没给就会引发一个错误。紧挨着就是我们如何在log记录里写一条消息。

然后我们建立了一个vals字典，用来使用其中的值来设置批量更新：the new date,new responsible,或者两者都有。然后在一个记录上使用写方法
#### Logging

#### Raising exceptions

#### Helper actions in wizards



### Working with the ORM API

#### Method decorators

#### Overriding the ORM default methods

#### Methods for RPC and web client calls

#### The shell command

#### The server environment

#### Modifying the execution environment

#### Transactions and low-level SQL



### Working with recordsets

#### Querying models

#### Singletons

#### Writing on records

#### Working with time and dates

#### Operations on recordsets

#### Manipulating recordsets

#### Using relational fields

#### Working with relational fields




### Summary




[back](../)



