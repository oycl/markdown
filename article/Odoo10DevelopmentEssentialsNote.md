# Odoo 10 Development Essentials Notes

## Odoo 10 Development Essentials
	权利声明，免责声明，商标说明

## Credits
	职责分工表

## Foreword
	Odoo特性：极简化，模块化，可扩展，可伸缩
	最新特性：Website特性，和第三方系统的集成机制

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

1. Chapter 1,Odoo开发环境安装，如何管理Odoo服务器实例
2. Chapter 2,建立你的第一个Odoo应用
3. Chapter 3,继承-扩展现有的应用
4. Chapter 4,模块数据-扩展标识符概念和数据导入导出
5. Chapter 5,模型-组织应用数据
6. Chapter 6,视图-设计用户界面
7. Chapter 7,ORM应用逻辑-支持商业流程
8. Chapter 8,书写测试和排错代码
9. Chapter 9,Qweb和Kanban视图
10. Chapter10,建立QWeb报告
11. Chapter11,建立Website前端特性
12. Chapter12,扩展API-和其它系统集成
13. Chapter13,部署检查清单

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

在root下工作被认为是一个坏习惯。据说，Odoo server 不会被root启动。但是，以我的实际使用效果看，没有root权限会很烦。具体内容详见[Odoo安装](OdooInstall.md#test-eng lish)

### Installing Odoo from the source




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
