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

As usual, we need to provide a distinctive name.We also need the model they operate on and the domain filter to use for the access restriction. The domain filter uses the usual list of tuples syntax used across Odoo.

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

### Better describing the module


### Summary





[back](../)
