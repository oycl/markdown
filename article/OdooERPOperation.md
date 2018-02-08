# OdooERPOperation

## 一，MRP Quality
步骤
1，安装质量控制app Quality Control App
2，建立控制点Control Points
3，做质量检测Quality Checks
4，产生质量报警Quality Alerts
5，用报告分析结构Reporting

## 二，Sales销售模块
### 1，建立客户

客户类型可选individual或者Company。在odoo10里面，individual和company都可以选多联系人。
在res.partner里面is_company是一个布尔字段，如果是公司则为TRUE
customer是一个布尔字段，勾选时在数据库里为TRUE
Vendor还没发现
在res.partner里面建立客户时，res.user里面并没有相应的数据，反过来应该是成立的。用的是委托继承，考虑一下m.part和product.template的关系。还有一种实现方法是数据库的触发器，同步两个表。


### 2，如果只安装了一个销售模块，在创建联系人的时候会提示
```xml
The following fields are invalid:
Account Receivable
Account Payable
```
需要再安装a chat of account(会计科目表)模块来解决这个问题。开始只安装了一个中国会计科目表，还是不好用。后来又安装了中国小企业会计科目表，就可以创建客户了。
在培训的时候可以说明，填表form时候，TAB移动到下一个字段，SHIFT+TAB移动到上一个字段。
如果是有关联的字段，需要一定的填写顺序。比如先填国家，再填省份。


### 3，建立产品
在表product.template建立条目而不是product.product
1. general information
Can be sold对应字段sale_ok
Can be Purchased对应字段purchase_ok
在没安装采购管理的时候,odoo10也可以指定产品是否能被采购了。

Product Type 目前有两个选项Service和consu，对应字段type
service不会建立采购订单，consumables是实际出售的，可以配置来生成采购单

Internal Reference可以输入公司内部编码

2. 在sales页面里什么也没有，随着应用的安装或者配置变化，里面的内容就不一样了，这在odoo里面很常见。

3. 在invoicing开发票页面，有客户税和供应商税两个选项，因为安装了会计科目，就自动填上了。odoo支持一个商品多个税种叠加。

invoicing policy
默认情况下，odoo配置发票，发票的line items将会从销售单中基于ordered quantities建立。这会导致即使没有发货，客户也会被开发票。反之，你可以改变在客户收到货物的时候开发票。如果你有一个在销售单的物品还没有投递，客户将不会被开发票。

### 4，建立sales order
在建立销售单的时候，product是可以建立的，如何限制销售部门不能建立产品。正常的产品应该研发中心给出号。

1. 理解销售单的工作流
开始是询价单，经过发送邮件或者打印报表变成订单

### 5, 开发票
开发票有几个选项，和定金(down payment)相关。
数据库表account_invoice_line存储了订单关联的订单项
表account_invoice存储了订单
在主菜单invoicing里面可以看到这个发票

随带讲了几句breadcrumb面包屑导航

## 三，CRM


## 四，采购模块

### 理解采购流程
完全有可能创建一个产品并在不实施采购系统的情况下出售。

* 建立供应商

* 建立库房位置:在odoo里每个位置有一个自己的地址，可以建立嵌套的子位置以便更好管理和报告

* 产生报价单和订单
为了获取原材料，你可能要先建立一个报价单发给供应商，在采购中，建立文档告诉供应商，哪个产品你需要，量是多少，期望花多少钱。通常也叫做采购。
依赖企业和公司的特别位置，可能有各种方法来管理报价和审批。

* 接收货物
在一个简单的采购工作流，一旦你的采购单被供货商接收，你将会等待执行订单。某时，你会收到产品。根据你的企业，日期可以是同一天，或者延长至月。当投递完成，你
将接收产品到你选择的位置。
现在产品接收完毕，你已经建立一个制造单。但是首先，让我们给供应商付款。

* 结清发票
一旦你收到产品，付钱只是时间问题。发票可以在你订购产品的时候，在产品发送给你的时候，或者你收到产品的时候。无论何时获得发票，您都可以确定，如果您收到产品，您最终将为其发票。

收到发票时，必须将其与采购订单的准确性进行比较。 采购订单和发票之间的任何差异必须在发票付清之前解决。 基本上，这是确保您只支付您授权购买的产品的方法。 最后，将收货或交货订单与采购订单和发票进行匹配也是很好的做法。

### 安装采购应用
Odoo的模块化可以只安装需要的应用。当安装完了采购应用后，将会多出采购和库房两个菜单，可见采购和库房的关系。

> 可能采购管理这个模块以及安装完了，比如电子商务模块就需要采购管理做依赖。

purchases菜单是一个你能建立报价单和采购单的地方。inventory菜单可以管理物理意义上的库房。如果你简单的看看这些菜单，你会发现有的子菜单会出现在两个菜单里，例如products

* 设定第一个供货商

Odoo还列出了您可以从供应商管理中获得的一些功能，例如跟踪讨论，购买历史和与供应商关联的文档。

事实上，客户也可以成为供应商，这是完全可以接受的。 创建新的供应商记录时，供应商复选框会在表单上的销售和采购页面下自动为您标记。 有时候这会让刚接触Odoo的人感到困惑。 本章将开始使Odoo中的公司，联系人，客户和供应商之间的关系更加清晰。

* 指定供应商公司与个人

就像你建立一个客户一样，在form的顶端需要选择公司还是个人。这次我们选择公司。

* 为采购配置产品

当我们建立第一个产品的时候，我们只关心给一个客户出售我们的产品。我们只给产品命名并且设置了售价。
为了从供应商处采购，我们必须提供更多一点的信息。为了达到这个目的，我们在采购标签下编辑产品信息。就是我们要把产品里inventory页面里的routes选择为Buy

供应链信息：在Odoo10里面，你有更多的选择管理你的采购供应链。幸运的是，Odoo默认设置了两种通用的路线：Buy和Make to Order。对采购来说，我们选择buy以便Odoo可以正确地把我们采购的商品从我们的供应商路由到我们的内部仓库。

Buy：当您配置buy路线时，您可以通过以下两种方式之一购买产品。 购买此产品的方法之一是创建采购订单并手动将产品添加到采购订单。 这是典型的手动采购系统，其中采购代理可能使用Odoo以外的其他事件或检查Odoo中的报告来创建采购订单。 

除了创建手动采购订单之外，还可以创建重新订购规则，当产品库存低于设定的最小值时，将自动创建采购订单草稿。 这种方法适用于频繁订购的产品，使您的采购经理不必为某些甚至大部分库存手动创建采购订单。

Make To Order：当您配置Make To Order路线时，您将告诉Odoo您希望在销售订单包含该特定产品时创建草稿采购订单，而不管手头有多少库存。 例如，即使您的库存中有2,000件产品，并且客户订购了10件产品，但如果您已选中了“到草稿采购单”路线，Odoo仍将为10个产品创建一个草稿采购订单。

通常情况下，企业在不需要保留产品库存的情况下会使用“生成草稿采购单”选项，在这种情况下一旦销售订单得到确认，就会生产或购买用于转售的产品。 当然，可以使用“购买”和“生成草稿采购单”的组合来重新订购限制，以建立一个始终保持库存最小数量的系统，这种情况下也会为包含该产品的销售订单创建草稿采购订单。 请记住，草稿订单总是可以取消，所以根据采购部门的流程，即使他们希望在Odoo中保持自己的最小和最大限制，他们也可能需要“生成草稿采购单”的行为。

* 采购信息

* 设置产品的价格

* 指定产品的供应商

* 建立第一个采购报价单

* 接收产品

* 给供应商付款

###总结



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
