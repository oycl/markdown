# 题目

## 一，标题
### 三，标题

### How to remove the 'Add an Item' quick insert option from a tree conditionally?
The 'Objective Group' tree belongs to a One2Many field linking the 'Objective Groups' model with the 'Job Plans' model that the view shows. Is there any way to disable this 'Add an Item' option conditionally?

For example on state 'draft' of 'Job Plans' i want this option to appear, but if state changes then the option should disappear.

```xml
<notebook>
  <page string="Objective Group">
    <field name="objective_group_ids">

      <tree string="Objective Group" create="true" edit="true" delete="true">
        <field name="state" invisible="1" default="draft"/>
        <field name="check_is_manager" invisible="1"/>
        <field name="name" attrs="{'readonly': [('state', 'not in', ['draft'])]}"/>
        <field name="description" attrs="{'readonly': [('state', 'not in', ['draft'])]}"/>
        <field name="weight" sum="Total" attrs="{'readonly': [('state', 'not in', ['draft'])]}"/>
        <field name="rating" readonly="1" attrs="{'invisible': [('state', 'in', ['draft','review'])]}" />
      </tree>
    </field>
  </page>
</notebook>
```

回答：you can make read-only one2many field according to the condition. add 
```xml
attrs="{'readonly':[('state' , '!=', 'draft')]}"
```
[链接](https://www.odoo.com/forum/help-1/question/how-to-remove-the-add-an-item-quick-insert-option-from-a-tree-conditionally-131213)

### product.template vs product.product

I'm doing some work on automatic supplier price list import.
product.product and product.template are sometimes confusing. Does anyone have a technical explanation of the difference?

回答：
1: product.template (Products Menu)
Actually it is not a product it is Product template就是说我们添加或者导入产品，都是操作template表，需要测试，涉及到委托继承。
whenever you are creating Product (product.template) than same will be created in also (product.product)
it contains the main product and with list of product variant (attributes)

2: product.product (Product Variants)
Here there are actual products with their variants as a separate products
When ever you are creating SO or PO than actually you are using Product variants not Products

3: Product Attributes
To create Product Variant you need add Product Attributes to that product which will automatically Create Product Variants 

   Example:
     there is an Product Named (Moto G)
     so by default it will be in product.template and product.product
     Now, if you want to add Variant than you need to create Attribute and add to that particular
     Imagine You have created Memory as an attribute and 16 GB and 32 GB as that attribute value than it will be total two Product in Product Variants
     You can Create Attribute From Product Categories & Attributes --> Attributes
     You can Create Attribute Values From Product Categories & Attributes --> Attribute Values

[参考](https://www.odoo.com/documentation/user/10.0/inventory/settings/products/variants.html)

[原文](https://www.odoo.com/zh_CN/forum/help-1/question/product-template-vs-product-product-94430)


### 不同库房由不同人员负责
#### 有个应用叫Stocks Access Rules
The app goal is to limit user access to a specified location. The tool let you better control movements and organize geographically distributed warehouse system
应用程序的目标是限制用户访问指定的位置。该工具可让您更好地控制移动并组织在不同地点的仓库系统。

Features 特征
A user would see only the locations and the stocks placed either on his/her locations and their children, or on the locations without accepted users stated  
用户只能看到库位和放置在他/她的库位及其子位置上的库存，或者没有声明哪个用户负责的库位上

Define users on the form of locations or locations on a the form of users. Both approaches would lead to the same results. Be cautious: if no user is defined on a location form, those locations and the related stocks would be visible for everybody
在位置的表单上定义用户，或者在用户的表单上定义位置。两种方法都会导致相同的结果。请谨慎：如果在位置表单上没有定义用户，那么这些位置和相关库存对每个人都是可见的

If a user has a right for this location, he/she has an access to all its child locations  
如果用户有权访问此位置，则他/她可以访问其所有子位置

The rights are controlled for locations, stock quants (available inventories), pickings, stock moves, picking types (operations)  
权限应用于位置，库存数量（可用库存），捡货，库存移动，捡货类型（操作）

The rules are for everybody except Administrator and users with special rights 'Super Warehouse Manager'. Those users would see all the locations disregarding settings  
除了管理员和拥有特殊权限'超级仓库经理'的用户之外，规则适用于每个人。这两个用户会看到所有位置，而无视设置

The app is fully compatible with other Odoo Core Apps, including Point of Sale (POS)
该应用程序是完全兼容其他Odoo核心应用程序，包括销售点（POS）


#### How to Restrict Warehouse for User ?
[文章地址](https://www.odoo.com/zh_CN/forum/help-1/question/how-to-restrict-warehouse-for-user-41621)

Hello Forum, Please let me know, how to restrict the User by warehouse in V7 ?

Suppose there are "n" number of warehouses, then How to assign a Warehouse for user, to restrict the access? So that, He will be accessible to only one Warehouse, its Locations and Products in that Warehouse- only.

回答：
In a new custom module do the following:

Create a security group per warehouse and then create a Rule for each group that restrict this group to see this warehouse only, then assign the required users to this group.

To implement my idea I created a module 'warehouse_filter'

I created for this purpose two warehouses 'warehouse 1' and 'warehouse 2'

I created two groups, one per warehouse, and a rule for each group with the domain using the warehouse name as a filter, I tested it and worked fine

Here is my work

```xml
<?xml version="1.0" ?>
<openerp>
    <data>
        <record model="ir.module.category" id="module_category_warehouse_filter">
            <field name="name">Warehouse Filter</field>
            <field name="sequence">80</field>
        </record>
		<record id="group_warehouse_1" model="res.groups">
			<field name="name">Warehouse 1</field>
			<field name="category_id" ref="warehouse_filter.module_category_warehouse_filter"/>
		</record>
		<record id="group_warehouse_2" model="res.groups">
			<field name="name">Warehouse 2</field>
			<field name="category_id" ref="warehouse_filter.module_category_warehouse_filter"/>
		</record>
		<record id="rule_for_warehouse_1" model="ir.rule">
			<field name="name">Rule for Warehouse1</field>
			<field ref="stock.model_stock_location" name="model_id"/>
			<field name="domain_force">[('name','=','warehouse 1')]</field>
			<field name="groups" eval="[(4, ref('warehouse_filter.group_warehouse_1'))]"/>
		</record>
		<record id="rule_for_warehouse_2" model="ir.rule">
			<field name="name">Rule for Warehouse2</field>
			<field ref="stock.model_stock_location" name="model_id"/>
			<field name="domain_force">[('name','=','warehouse 2')]</field>
			<field name="groups" eval="[(4, ref('warehouse_filter.group_warehouse_2'))]"/>
		</record>
    </data>
</openerp>

```
I hope this idea be useful.

This is the main idea, but it could be implemented better by create a new field in the stock.location model called 'wh_type', and filter by this field, not by the warehouse name, so several locations may take one type.


#### Multi warehouses access rights?
[文章地址](https://www.odoo.com/zh_CN/forum/help-1/question/multi-warehouses-access-rights-77384)

My client has a company with the following structure:

The HO                               (HO)
The Manufacturing Plant              (MF)
Other warehouses                     (OW)
Each of these branches has its own local warehouse as described, respectively.

The warehouse access rights should be distributed over these warehouses, as each stock keeper should only deal with his warehouse.

In Odoo Sales, each sales team member can be restricted to see only his leads.
However, in Odoo Warehouse, I cannot see such access rights.

Please help with your experience.

回答1：
You may use these record rules;
```xml
#HO

['&', ('location_id', 'not ilike', 'OW'),('location_dest_id','not ilike', 'OW'), ('location_id', 'not ilike', 'MF'),('location_dest_id','not ilike', 'MF')]

#OW

['&', ('location_id', 'not ilike', 'HO'),('location_dest_id','not ilike', 'HO'), ('location_id', 'not ilike', 'MF'),('location_dest_id','not ilike', 'MF')]

#MF

['&', ('location_id', 'not ilike', 'OW'),('location_dest_id','not ilike', 'OW'), ('location_id', 'not ilike', 'HO'),('location_dest_id','not ilike', 'HO')]
```
回答2：
Hi,

create 3 access rights groups, one by branch.group_ho, group_mf an group_ow.

search for ir_rules define for object stock.warehouse. Override it by adding first group group_ho and by overriding domain by adding filter with the xml_id/id of warehouse The HO.

Create 2 other ir_rules like previous, just change the group an domain with group_mf/The Manufacturing Plant and group_ow/Other warehouses.

For each group, add user you want to determine a warehouse.

Bye

### 在打印“会计-报告-业务伙伴分类账”的时候出错
[文章地址](https://github.com/odoo/odoo/issues/24881)
提到解决方法：在odoo10/addons/account/report/account_partner_ledger.py的第38行
r['date'] = datetime.strptime(r['date'], DEFAULT_SERVER_DATE_FORMAT).strftime(date_format'))

修改成
r['date'] = datetime.strptime(r['date'], DEFAULT_SERVER_DATE_FORMAT).strftime(date_format.encode('utf-8'))

但是经过实际测试，改完后在windows环境下会直接引起python的崩溃，在linux下没有试验

还有另一个解决方法就是在“设置-翻译-语言”里面，选择“Chinese (CN) / 简体中文”，在“日期格式 %Y年%m月%d日”里面，把年月日三个字删除掉

不知道此bug在github上后续还有没有fix



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
