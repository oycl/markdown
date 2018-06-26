# 库房系统安装及开发

## 一，安装
### 以框架+ultra+现用用户为基础，进一步安装各个模块的方法（不使用，因为删除演示数据未必能删干净）
1. 在PgAdmin中新建一个空数据库，改名为Odoo10，使用从运行系统最新备份中拷贝出的odoo10.custom恢复回去，

> 如果使用原数据库名字odoo10，则不会出现丢失图标的问题，如果换一个新的数据库名字如forchart则图标不能正确显示，重新安装一遍应用，应用涉及的图标会变正常，但是系统图标“设置”，“应用”还是不行。 解决方法：重新安装base模块后系统图标正常。

2. 想不安装产品和库存的示例数据，但是使用了各种without_demo=都没有效果，只好到product和stock模块里面把demo和里面的行都注释掉，这下清净了。但是在安装后面的应用时问题又来了，后面应用的demo数据要用到product和stock里demo的ID，所以干脆都装上吧。整个demo织了一张网，不大好删除。

3. 依次安装，“库存”，“采购”，“销售”，“会计”，“质量”，“联系人”，
都装完以后开始删除，顺便把表之间的关系也理顺一下。应该先删除销售单，采购单，及库房的单据，然后删除产品，供应商和客户。

4. 安装“会计”应用。应用会自动找到“中国会计科目表”（技术名称l10n_cn）并安装。细节参照对会计科目的分析。因为目前运行系统为中文环境，所以自动安装的是“中国会计科目表”

> 但是在“会计仪表板”中，提示“这是会计仪表板，如果你还没有安装科目表，请先安装一个 浏览可用的国家”，而安装了应用“中国小企业会计科目表”（l10n_cn_small_business）的系统，在仪表板内有内容。
> 于是复制一份“中国小企业会计科目表”进行改写，具体改写步骤参照OdooTable.md

### 先安装框架，然后各个模块，最后自有模块（ultra+invku）
#### 开发阶段
1. 创建一个空白的数据库invbase，不要选中“装入演示数据”检查框（Load Demonstration data）,因为我们要创建的是一个空白数据库，而不是演示数据库，这一步非常重要，很多人都在此出了问题。

注：如果使用配置文件建立数据库，把配置文件改为db_name = invbase，加入without_demo = all。数据库存档00invbaseInit.backup

2. 首先安装“会计与财务”模块，先装中国会计科目表
信息设置：手写公司信息，公司图标指定。
公司名称：一拖（黑龙江）东方红工业园有限公司
公司标语：新的生活从东方红开始
街道：建华区北苑开发区东方红路1号
国家：china（选择）省份：黑龙江（选择）城市：齐齐哈尔市 邮编：161000
网站：http://www.ythlj.com.cn
电话：0452-2380345
传真：0452-2380066
Email：info@yto.com

数据库存档01invbaseCaiwuNoChart.backup

3. 再装一拖会计科目表。数据库存档02invbaseCaiwu.backup
4. 然后再安装质量(库存)，销售，采购，模块。安装上述模块后，部分菜单可能为英文，你应该点击菜单“设置→翻译→加载翻译”，把自己的语言改为简体中文（重启Odoo服务）。这样，你就得到了一个能实现本章需求的最小系统
数据库存档03InvbaseMini.backup

5. 安装主题界面，安装hide_inbox，数据库存档04InvbaseWeb.backup

6. 在04InvbaseWeb.backup基础上测试安装制造，如果成功存档05InvbaseWebMrp.backup

7. 退回04InvbaseWeb.backup，导入测试群组和测试用户信息，给用户分配角色，批量统一设置密码为1。数据库存档06InvbaseWebUser.backup

8. 继续安装mrp（新方案带mrp），安装ultra，调整群组和用户的关系，

修改前缀
“设置→技术→序列与标识符→序号” 里面
INV改成发票
BILL改成账单
PO改成采购单
SO改成销售单
MO改成制造单
BNK改成银行
CSH改成现金

把会计改成财务,不用改代码,在开发者模式下,选择翻译->已翻译术语，搜索“翻译值：会计”，找到“已翻译字段”为 ir.ui.menu,name，改第二个翻译值为财务。

9. 完成后存档07InvbaseAll.backup

10. 安装stock_analytic模块

11. 安装invku，对invku进行开发并测试，使其满足要求

12. 日历是一个模块，可以进入日历视图；联系人也是一个模块；仪表板是一个应用，也需要安装，也可以定制
上面这三个模块如果需要测试，可以从03InvbaseMini.backup出发安装测试

附备份命令（windows）
pg_dump.exe --host localhost --port 5432 --username "postgres" --no-password  --format custom --blobs --verbose --file "D:\odoo\backups\name.backup" "invbase"


#### 测试阶段
在192.168.4.5:80上测试，编码，改进，和财务和其它部门确认，无误后进行培训


#### 试运行阶段
选一个周末加班，在1.55生产环境上部署试运行环境，因为1.55已经有完备的每日备份计划（数据库继续使用odoo10）可以充分利用
1. 把前一天的数据库自动备份拿到开发环境上测试一下，看有没有问题。没有问题后封存，防止本次搭建失败，正常上班后没有系统可用。
2. 导出m.part和m.assemble。m_designer,m_important,m_sheet,m_type
3. ultra，invku和yto科目表的最新版download到1.55生产环境中，
4. 按照上面的方法在1.55上重新安装一遍系统模块。
5. 安装ultra，导入m.part和m.assemble，运行数据库函数（验证m.part和m.assemble还有m.bom和1中保存的数据是否一致），无缝过度。
6. 安装invku
7. 重置一次所有人的密码为1，如果已经拿到实际的权限列表，挨个人员进行权限分配和测试，如果没有就都设置成员工。
8. 在其他人的电脑测试系统页面是否显示正常。
9. 当日的系统备份作为一个原始存档，除ultra外无运行数据。以后可以用来做各种测试
10. 整个系统可以一直运行，等到了下一个财政年度会自动重新开始编码，在年终可以配合盘点输入一次数据。



在“”一节，""选项里面，选择“”
## 二，开发
### 模块配置
#### 采购
通过菜单“采购→配置→设置”
1. 供应商价格管理方式
在“采购订单”一节，"供应商价格"选项里面，选择“允许使用和导入供应商价格表”

2. 修改订单是否允许
在“物流”一节，"采购订单修改"选项里面，选择“允许修改采购订单”

3. 库存计价采用实际价格法
在“采购订单”一节，“成本方法”选项里面，点选“使用'固定价','实际价'或者'平均价'成本定价方法”

我们在产品类别里面做测试，实际上总类的改变不影响计价的方法，在子类里面设置“成本计算方法”才会生效

4. 翻译修改
可以把采购单的“供应商参照”改为“供应商合同”，提高利用率
在翻译中搜索“供应商参照”，改第三个为“供应商合同”

#### 销售
通过菜单“销售→配置→设置”
1. 计量单位配置
在“产品”一节，"供应商价格"选项里面，勾选“一些产品可使用不同的销售/采购单位(高级)”，这时在采购和销售的订单行里面就会有计量单位一栏。注意这个设置在采购和销售里面都可以设定

2. 修改销售单是否允许
在“报价单 & 销售”一节，"销售订单修改"选项里面，选择“允许在“销售订单”菜单中编辑销售订单(不通过报价单菜单)”


#### 库房
通过菜单“库房→配置→设置”
1. 仓库位置设置
在“位置和仓库”一节，"仓库和位置使用水平"选项里面，选择“管理 由多个库存位置组成的仅仅1个仓库”

2. 入库和出库自动在日记账分录中记录
在两个位置设置
1，在“库存会计”一节，"库存计价"选项里面，选择“永续库存计价（库存移动生成会计分录）”
2，采购或者库存应用里-》配置-》产品类别里面 “库存计价”里面的库存计价选择永续（自动）
会计库存属性里面的科目表配置好

如果不使用这种方法，在入库确认和出库确认时不会自动产生日记账分录。需要点击开”供应商账单“或者”客户发票“时才能产生日记账分录。

#### 财务





### 权限配置

1. 每种用户原生能进行的操作如下：
如果库存是管理员：则强制质量是用户，会计是开单
如果库存是用户：则强制质量是用户
会计中，顾问的权限涵盖会计师，会计师的权限涵盖开单

#### 客户/供应商信息管理部分 res.partner
原始权限：
在销售和采购中，只有管理员有权限增加客户和供应商信息，普通用户没有
仓库管理员也有权限增加信息
财务中即使顾问也没有权限增加客户/供应商信息。

目标：
客户/供应商信息的创建分别归销售和采购的管理员，只取消他们删除的权限就行
仓库管理员取消创建，修改和删除权限，只保留读权限
增加财务顾问修改信息的权利，比如销售和采购创建的名称错误可以修改

使用方法：
权限配置的格式是
扩展ID，名称，模型ID，群组ID，读，写，创建，删除

下面这段xml仅存档用，因为系统的设置和下面的写法是一样的
```xml
下面应该是一条就可以解决问题，实际在系统模块中就存在两条数据，只有都改才行。
取消销售管理员对res.partner的删除权限
sale.access_res_partner_sale_manager,res.partner.sale.manager,base.model_res_partner,sales_team.group_sale_manager,1,1,1,0
sale.access_product_group_res_partner_sale_manager,res_partner group_sale_manager,base.model_res_partner,sales_team.group_sale_manager,1,1,1,0

取消采购管理员对res.partner的删除权限
purchase.access_res_partner_purchase_manager,res.partner.purchase.manager,base.model_res_partner,purchase.group_purchase_manager,1,1,1,0
purchase.access_product_group_res_partner_purchase_manager,res_partner group_purchase_manager,base.model_res_partner,purchase.group_purchase_manager,1,1,1,0
```

取消库存管理员对res.partner的创建和写权限
stock.access_product_group_res_partner_stock_manager,res_partner group_stock_manager,base.model_res_partner,stock.group_stock_manager,1,0,0,0

取消额外的权利/联系人创建 的删除权限
base.access_res_partner_group_partner_manager,res_partner group_partner_manager,base.model_res_partner,base.group_partner_manager,1,1,1,0

取消制造管理员的res.partner的创建和写权限
mrp.access_product_group_res_partner_mrp_manager,res_partner group_mrp_manager,base.model_res_partner,mrp.group_mrp_manager,1,0,0,0

增加财务顾问的修改权利
account.access_res_partner_group_account_manager,res_partner group_account_manager,base.model_res_partner,account.group_account_manager,1,1,0,0

方案A
把上面5个可以创建和写的权限取消后，就剩下一个群组：额外的权利/联系人创建，对res.partner有创建和写的权利。需要让哪个用户有这个权利就把这个用户添加到该群组中。

方案B
取消库存和制造管理员的创建和写的权利后，销售和采购管理员和额外的权利/联系人创建三种人具有创建和写的权利,财务顾问具有写权利。


上面只是解决了谁有权限增删的问题，下面修改各看各的信息
```xml
        <record id="base.action_partner_supplier_form" model="ir.actions.act_window">
            <field name="domain">[('supplier','=',True)]</field>
        </record>

        <!-- 这里面要注意这个action不是和supplier对应的action_partner_customer_form，而是action_partner_form"-->
        <!-- action_partner_customer_form有什么用处还不知道-->
        <record id="base.action_partner_form" model="ir.actions.act_window">
            <field name="domain">[('customer','=',True)]</field>
        </record>
```

#### 产品信息部分 product_product，product_template
##### 普通用户

因为需要普通用户（base.group_user）查看库存的在手数量，所以单独建立了一个菜单“技术”调用“产品”页面

一个普通用户进入这个“产品”页面首先遇到的问题：提示stock.move这个模型没有权限所以看不了，这就需要给stock.move这个模型添加普通用户的读权限。
access_stock_move_group_user,stock_move group_user,stock.model_stock_move,base.group_user,1,0,0,0

添加完毕后，可以看到产品页面，但是点击无法进入（stock.warehouse.orderpoint的权限原因），这正好符合需要，隐藏里面的信息。

注意这里面的在手数量，专指WH/库存里面的数量，分配到生产库存的的不能称作在手数量，这种计算方式正好符合我们的需要。

在这个tree view中想隐藏销售价格，和成本字段，因为使用“实际价格”来计算库存估值，所以销售价格和成本都不使用。
使用了视图的继承属性，结果怎么试验都有错误，还怀疑原始视图被别的视图继承后不能再继承，实际上是__manifest__.py里面的依赖没有加上，加上依赖   'depends':['base','product','sale','account','purchase'],解决问题


#####财务角色
在测试过程中，作为财务顾问，虽然有权限去写上面这两个模型，但是在界面中点击已经存在的产品时会弹出这个提示框：

对不起，您没有访问此文档的权限。 只有以下访问等级的用户可才做这:
- Inventory/User
- Inventory/Manager
- Sales/User: Own Documents Only
- Other Extra Rights/Portal
- Purchases/User
(单据模型: stock.warehouse.orderpoint)

解决方法从stock.warehouse.orderpoint入手，如果把这个模型加入到财务开单的可访问的模型中，也可以解决问题，个人认为财务有必要访问具体的产品信息，所以添加如下访问规则
access_stock_warehouse_orderpoint,stock.warehouse.orderpoint,stock.model_stock_warehouse_orderpoint,account.group_account_invoice,1,0,0,0

取消财务顾问对产品的创建和删除权限，保留读和写权限，而默认会计师和开单只有读权限，不能修改
account.access_product_product_account_manager,product.product.account.manager,product.model_product_product,account.group_account_manager,1,1,0,0
account.access_product_template_account_manager,product.template.account.manager,product.model_product_template,account.group_account_manager,1,1,0,0

#####库房角色
库房用户，只保留读权限
stock.access_product_product_stock_user,product_product_stock_user,product.model_product_product,stock.group_stock_user,1,0,0,0
stock.access_product_template_stock_user,product.template stock user,product.model_product_template,stock.group_stock_user,1,0,0,0

库房管理员，只保留读权限
stock.access_product_product_stock_manager,product.product stock_manager,product.model_product_product,stock.group_stock_manager,1,0,0,0
stock.access_product_template_stock_manager,product.template stock_manager,product.model_product_template,stock.group_stock_manager,1,0,0,0

#####销售角色
销售管理员，只保留读权限
sale.access_product_product_sale_manager,product.product salemanager,product.model_product_product,sales_team.group_sale_manager,1,0,0,0
sale.access_product_template_sale_manager,product.template salemanager,product.model_product_template,sales_team.group_sale_manager,1,0,0,0


#####采购角色
采购管理员，只保留读权限
purchase.access_product_product_purchase_manager,product.product purchase_manager,product.model_product_product,purchase.group_purchase_manager,1,0,0,0
purchase.access_product_template_purchase_manager,product.template purchase_manager,product.model_product_template,purchase.group_purchase_manager,1,0,0,0

按照上面的方法只保留两个模型的读权限，那么在product_supplierinfo模型，也就是“产品”-》“库存”页面里面的供应商信息将不能添加，只有放开product_template里面的写权限，才能针对产品添加供应商信息。也许会把这个权限放给采购。

#### 供应商价格部分 product_supplierinfo
取消库存管理员的创建，写和删除权限
stock.access_product_supplierinfo_stock_manager,product.supplierinfo stock_manager,product.model_product_supplierinfo,stock.group_stock_manager,1,0,0,0

取消销售管理员的创建，写和删除权限
sale.access_product_supplierinfo_sale_manager,product.supplierinfo salemanager,product.model_product_supplierinfo,sales_team.group_sale_manager,1,0,0,0

取消采购管理员的删除权限
purchase.access_product_supplierinfo_purchase_manager,product.supplierinfo purchase_manager,product.model_product_supplierinfo,purchase.group_purchase_manager,1,1,1,0

取消制造用户的创建，写和删除权限
mrp.access_product_supplierinfo_user,product.supplierinfo user,product.model_product_supplierinfo,mrp.group_mrp_user,1,0,0,0


产品的几维属性
1，来源，供货方式：购买或者自制
2，存在形式：服务或者可以库存
3，用途：可用于销售，可用于采购（简单起见，所有零件都勾选这两个选项）
4，会计属性：成本及销售价格，收入科目，费用科目，销项税，进项税


产品分类定义收入和费用科目作为大的全局设定，具体产品如果指定科目就可以代替默认产品分类中指定的科目
销项税和进项税在会计科目表安装的时候也会指定默认值。供应商中定义了税率，则使用供应商税率，如果在产品中也指定了税率，则使用产品的税率。


取消库存，销售，采购，制造管理员对产品计量单位和产品类别的创建，写和删除访问。
#### 产品信息之计量单位
stock.access_product_uom_categ_stock_manager,product.uom.categ stock_manager,product.model_product_uom_categ,stock.group_stock_manager,1,0,0,0
stock.access_product_uom_stock_manager,product.uom stock_manager,product.model_product_uom,stock.group_stock_manager,1,0,0,0

sale.access_product_uom_categ_sale_manager,product.uom.categ salemanager,product.model_product_uom_categ,sales_team.group_sale_manager,1,0,0,0
sale.access_product_uom_sale_manager,product.uom salemanager,product.model_product_uom,sales_team.group_sale_manager,1,0,0,0

purchase.access_product_uom_categ_purchase_manager,product.uom.categ purchase_manager,product.model_product_uom_categ,purchase.group_purchase_manager,1,0,0,0
purchase.access_product_uom_purchase_manager,product.uom purchase_manager,product.model_product_uom,purchase.group_purchase_manager,1,0,0,0

mrp.access_product_uom_categ_mrp_manager,product.uom.categ mrp_manager,product.model_product_uom_categ,mrp.group_mrp_manager,1,0,0,0
mrp.access_product_uom_mrp_manager,product.uom mrp_manager,product.model_product_uom,mrp.group_mrp_manager,1,0,0,0

#### 产品信息之产品类别
stock.access_product_category_stock_manager,product.category stock_manager,product.model_product_category,stock.group_stock_manager,1,0,0,0
sale.access_product_category_sale_manager,product.category salemanager,product.model_product_category,sales_team.group_sale_manager,1,0,0,0
purchase.access_product_category_purchase_manager,product.category purchase_manager,product.model_product_category,purchase.group_purchase_manager,1,0,0,0
mrp.access_product_category_mrp_manager,product.category mrp_manager,product.model_product_category,mrp.group_mrp_manager,1,0,0,0

#### 销售单信息部分
增加领导的访问权
invku_sale_order_read,sale order read,sale.model_sale_order,base.group_jingying,1,0,0,0
invku_sale_order_line_read,sale order line read,sale.model_sale_order_line,base.group_jingying,1,0,0,0

#### 采购单信息部分
增加领导的访问权
invku_purchase_order_read,purchase order read,purchase.model_purchase_order,base.group_jingying,1,0,0,0
invku_purchase_order_line_read,purchase order line read,purchase.model_purchase_order_line,base.group_jingying,1,0,0,0

#### 物料清单有权增加修改，给base.group_chanpin群组
invku_product_all,product all,product.model_product_product,base.group_chanpin,1,1,1,0
invku_product_temp_all,product temp all,product.model_product_template,base.group_chanpin,1,1,1,0
invku_product_enter,product enter,stock.model_stock_warehouse_orderpoint,base.group_chanpin,1,1,1,0


#### 解决库存是管理员：则强制质量是用户 和 库存是用户：则强制质量是用户
在质量用户中删除库存管理员和库存用户，进行测试
目前看要解决quality.check模型的访问问题
quality.access_quality_check_stock_user,quality.check,quality.model_quality_check,stock.group_stock_user,1,1,1,0


#### 解决库存是管理员：则强制会计是开单
在会计-开单用户中删除库存管理员，进行测试，目前还没发现报错的情况，有了再解决。


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
