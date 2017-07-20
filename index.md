---
layout: default
---

# [](#header-1)Building a Module 构建模块
    
    
> **Warning**
>
> **警告**
>
> This tutorial requires having installed Odoo
>
> 本教程需要安装Odoo

## [](#header-2)Start/Stop the Odoo server 启动/停止Odoo服务

Odoo uses a client/server architecture in which clients are web browsers accessing the Odoo server via RPC.

Odoo 使用 C/S架构，客户端即Web浏览器通过RPC协议访问Odoo服务器。

Business logic and extension is generally performed on the server side, although supporting client features (e.g. new data representation such as interactive maps) can be added to the client.

业务逻辑和扩展通常在服务端执行，而只有在添加客户端支持的新特征才会在客户端添加代码（例如，在交互映射中新数据的表示）

In order to start the server, simply invoke the command odoo-bin in the shell, adding the full path to the file if necessary:

为了启动服务器，只需要在shell中调用命令odoo-bin，如果需要则加入完整的路径名。

`odoo-bin`

The server is stopped by hitting Ctrl-C twice from the terminal, or by killing the corresponding OS process.

通过连发两次 Ctrl-C 命令或杀死相应的系统进程来停止Odoo服务

## [](#header-2)Build an Odoo module 构建模块

Both server and client extensions are packaged as modules which are optionally loaded in a database.

服务端扩展和客户端扩展都被封装为模块，这些模块可选择性的被安装，安装完成后通过数据库来加载。

Odoo modules can either add brand new business logic to an Odoo system, or alter and extend existing business logic: a module can be created to add your country's accounting rules to Odoo's generic accounting support, while the next module adds support for real-time visualisation of a bus fleet.

模块即可以是全新的业务逻辑，也可以是更改和扩展已有的业务逻辑。比如创建一个中国会计模块，将中国的会计准则添加到Odoo的通用会计中，也可以创建一个全新的实时可视化管理车队的模块。

Everything in Odoo thus starts and ends with modules.

Odoo中的所有功能都是包含在模块中。


### [](#header-3)Composition of a module 模块的组成
An Odoo module can contain a number of elements:

Odoo模块包含多个部分：

**Business objects**

业务对象

Declared as Python classes, these resources are automatically persisted by Odoo based on their configuration

Python类，这些类会被Odoo框架自动持久化，持久化的方式决定于类的定义。

**Data files**

数据文件

XML or CSV files declaring metadata (views or workflows), configuration data (modules parameterization), demonstration data and more

包括元数据（视图或工作流）、配置数据（模块参数）、演示数据等，以XML或CSV文件定义。

**Web controllers**

Web控制器

Handle requests from web browsers

处理Web浏览器的请求

**Static web data**

静态页面数据

Images, CSS or javascript files used by the web interface or website

网站或界面使用的图片、CSS或JavaScript文件

### [](#header-3)Module structure 模块的结构
Each module is a directory within a module directory. Module directories are specified by using the `--addons-path` option.

每个模块都是模块目录中的一个子目录。可以通过 `--addons-path` 选项指定模块目录的路径。

> **Tip**
>
> **提示**
>
> most command-line options can also be set using a configuration file
>
> 大多数命令行选项可以通过配置文件进行设置

An Odoo module is declared by its manifest. See the manifest documentation about it.

Odoo模块由清单文件进行声明。查看清单文件文档了解详细信息。

A module is also a Python package with a `__init__.py` file, containing import instructions for various Python files in the module.

模块是一个包含`__init__.py`文件的的Python包，`__init__.py`文件包含了模块需要的导入的各Python文件。

For instance, if the module has a single `mymodule.py` file `__init__.py` might contain:

例如，如果模块中包含`mymodule.py`文件，`__init__.py`应该这样写：

```python
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
from . import mymodule
}
```











## [](#header-2)Basic views 基本视图






## [](#header-2)Relations between models 模型关联





## [](#header-2)Inheritance 继承






## [](#header-2)Computed fields and default values 计算字段和默认值






## [](#header-2)Onchange 






## [](#header-2)Model constraints 模型约束





## [](#header-2)Advanced Views 高级视图





## [](#header-2)Workflows 工作流






## [](#header-2)Security 安全






## [](#header-2)Wizards 向导




## [](#header-2)Internationalization 国际化






## [](#header-2)Reporting 报告




## [](#header-2)WebServices Web服务




Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](another-page).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# [](#header-1)Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## [](#header-2)Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### [](#header-3)Header 3

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

#### [](#header-4)Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### [](#header-5)Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### [](#header-6)Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
