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



##### Setting values for relation fields




#### Shortcuts for frequently used models

#### Other actions in XML data files

##### Deleting records

##### Triggering functions and workflows


### Summmary


[back](../)

