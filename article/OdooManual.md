# odoo应用手册

## 第一部分 Odoo起步
### 第1章安装和初始设置
1. 数据库服务和 Odoo服务可被安装到同一台服务器上，如果出于性能的考虑，也可分布到不同的计算机上。
测试odoo服务1+PG数据库
odoo服务2------|
用一个用户名试验读写。

2. 安装ubuntu时可以将国外或香港的网址如“ http://hk.archive.ubuntu.com”替换为“http://mirrors.163.com”，然后在终端中执行下列命
3. 查看8069端口由哪个程序使用PID：sudo lsof –i:8069
4. 查看本机端口使用情况：sudo netstat –anpt
5. 每一个独立核算的企业都有一套相互关联的账簿体系，把这一套完整的账簿体系建立在计算机系统中就称为一个数据库。一般一个企业只用一个数据库。如果企业有几个下属的独立核算的实体，也可以为每个实体单独创建数据库。
6. 如果你安装了“website”模块后，登录界面的“数据库管理”菜单将不再显示，需要手动输入“http://IP 地址或域名:Odoo 服务端口/web/database/manager”如“http://127.0.0.1:8069/web/database/manager”来进入数据库管理界面。
7. 你可以安装“users_ldap”模块，它通过 LDAP（轻型目录访问协议，一个系统标准）来管理用户的认证，利用它可连接多个 Odoo 数据库，多个数据库可以共享一个用户账号。
8. 设置公司信息，现在币别默认为 EUR，现在不用管它，等后面安装好中国会计科目表后，币别会自动改为CNY。
9. 建议新建数据库后首先安装“会计”模块，点击“会计”图标下面的“安装”按钮。此时会多出一个“安装特性”选项卡，显示安装中创建的菜单和定义的报表。会计模块可以设置全局默认进项税16%和销项税11%。
10. 模块安装完成后，为了安装会计科目表，我们需要通过菜单“会计→配置→设置”，在科目一览表一节，点击“安装其它会计科目表”。注意在安装会计科目表前，请不要新建或编辑科目表，否则会造成安装的科目表不能显示。
11. 尽管卸载模块功能工作良好，但并不完美。它并不能保证精确的返回到未安装此模块之前的状态。因此在安装新模块之前，建议你备份数据库，这样你可以测试新模块并决定它们是否适合。如果它们不适合，你可以返回到你的备份。
12. 安装了一个新模块（默认情况下这些模块中的新的英文名词没有被翻译），或者更新了汉化包后，你需要通过“关于Odoo”菜单来激活开发者模式，然后使用“设置→翻译→加载翻译”，选择语言“Chinese(CN)/简体中文”，并勾选“覆盖已存在的术语”，最后点击“加载”按钮，来重新装入中文的翻译文件，为使界面完全汉化，你需要重新启动 Odoo 服务（如果不方便重启 Odoo服务，你可以尝试在首选项中先切换为英文，点击“应用”按钮后，再切换回中文）。
13. 因系统默认的报表字体不包含中文，因此打印中文报表时中文会是乱码。为此，在公司信息设置窗体中(参照1.4.1.2节)的“报表设置”选项卡中，“配置（RML）”一节的字体字段，点击右侧的“（重新加载字体）”。如果在字体列表里没有需要的中文字体，就需要首先安装中文字体：sudo apt‐get install ttf‐wqy‐zenhei
页眉和页脚也可以编辑，应该去除公司图标。
14. Odoo使用werkzeug 作为 web 服务的框架，性能比之前的cherrypy 有了很大的改善。但无论是werkzeug 还是cherrypy，都不是专门的web 服务器。通常的做法是在Odoo 之前加一个Nginx、Apache 或其它服务器。
15. Ngnix.conf里面gzip添加一个类型 application/javascript
16. 修改Nginx的default文件vi /etc/nginx/sites‐available/default，内容对比手册
17. 重启 Odoo 服务后，需要重启下Nginx，此代理才能正常工作。
18. 常见错误
CPU limit out，不要急着升级 CPU，修改odoo.conf 里的 limit_time_cpu 即可。
Nginx504 错误，修改Nginx 配置里的timeout 时间。
Nginx 502 错误，跟Nginx 无关，修改odoo.conf 里的limit_time_real 即可
19. 启用多核来提升Odoo 性能需要安装一堆模块，待验证。
20. 首先使用 df 命令看硬盘，假设你的磁盘是 sda，则执行sudo blockdev setra 4096 /dev/sda
这里4096 是内存从磁盘上读取文件的缓冲区大小
21. postgresql 数据库优化。shared_buffers：这是最重要的参数

### 第2章Odoo的基本操作
1. 点击菜单“设置→技术→动作→设置向导”，可显示设置向导的动作、类型和状态
2. “联系人”的多种联系方式。请通过菜单“销售→设置→设置”，在“报价和销售订单”一节的客户地址字段勾选“在销售订单上显示三个字段：客户、开票地址、发货地址”。然后在联系人窗体里新建或编辑联系人时，你就可以选择地址类型。
3. 通过菜单“销售→设置→设置”，在“产品”一节和“销售价格”一节分别勾选“某些产品可以使用不同的计量单位进行销售或采购（高级）”和“同一产品对不同客户有不同价格”，如下图所示：
4. 系统默认的每页显示条目数为 80。点击你可以点击此部位，然后直接输入要显示的起始条目和终止条目。
5. 对于你先前做过的类似文档，如产品、订单、发票、发货单等，你可以复制一个在此基础上修改，而不必每次都从头录入。
7. domain 用于自定义的搜索条件，主要用于自定义权限管理中的记录规则，其中的单个条件是一个三个元素组成的元组。

### 第3章应用到你的公司
1. 为了简化程序，并没有安装仪表板
2. 激活“开发者”模式后，在“群组”中，既可以设置访问权限，又可以管理菜单。所有用户都有权限查看的菜单在这里不显示
3. 发票的编码方法可以通过菜单中“设置→技术→序列与标识符→序列”来修改，默认情况下使用“INV/年/序号”

4. 测试每种用户原生能进行的操作
如果库存是管理员：则强制质量是用户，会计及财务是开单
如果库存是用户：则强制质量是用户

财务中，顾问的权限涵盖会计师，会计师的权限涵盖开单

#### 客户/供应商信息管理部分 res.partner
原始权限
在销售和采购中，只有管理员有权限增加客户和供应商信息，普通用户没有
仓库管理员也有权限增加信息
财务中即使顾问也没有权限增加客户/供应商信息。

应对策略
取消库存管理员对res.partner的创建和写权限
stock.access_product_group_res_partner_stock_manager,res_partner group_stock_manager,base.model_res_partner,stock.group_stock_manager,1,0,0,0

取消销售管理员对res.partner的创建和写权限
sale.access_res_partner_sale_manager,res.partner.sale.manager,base.model_res_partner,sales_team.group_sale_manager,1,0,0,0
sale.access_product_group_res_partner_sale_manager,res_partner group_sale_manager,base.model_res_partner,sales_team.group_sale_manager,1,0,0,0

取消采购管理员对res.partner的创建和写权限
purchase.access_res_partner_purchase_manager,res.partner.purchase.manager,base.model_res_partner,purchase.group_purchase_manager,1,0,0,0
purchase.access_product_group_res_partner_purchase_manager,res_partner group_purchase_manager,base.model_res_partner,purchase.group_purchase_manager,1,0,0,0

这样把上面5个可以创建和写的权限取消后，就剩下一个群组：额外的权利/联系人创建，对res.partner有创建和写的权利。
需要让哪个用户有这个权利就把这个用户添加到该群组中。

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

#### 产品信息管理部分 product_product，product_template
#####财务角色
在测试过程中，作为财务顾问，虽然有权限去写上面这两个模型，但是在界面中点击已经存在的产品时会弹出这个提示框：

对不起，您没有访问此文档的权限。 只有以下访问等级的用户可才做这:
- Inventory/User
- Inventory/Manager
- Sales/User: Own Documents Only
- Other Extra Rights/Portal
- Purchases/User

(单据模型: stock.warehouse.orderpoint)


解决方法一是把Other Extra Rights/Portal组中增加财务顾问（还需要具体关注这个Other Extra Rights/Portal的具体权限不能过大，因为工艺想用这个组来创建，编辑和修改产品模型），不推荐使用
解决方法二从stock.warehouse.orderpoint入手，如果把这个模型加入到财务开单的可访问的模型中，也可以解决问题，个人认为财务有必要访问具体的产品信息，所以添加如下访问规则
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


产品的几维属性
1，来源：购买或者自制
2，存在形式：服务或者可以库存
3，用途：可用于销售，可用于采购（简单起见，所有零件都勾选这两个选项）
4，会计属性：成本及销售价格，收入科目，费用科目，销项税，进项税


产品分类定义收入和费用科目作为大的全局设定，具体产品如果指定科目就可以代替默认产品分类中指定的科目（需要测试）
销项税和进项税在会计科目表安装的时候也会指定默认值。供应商中定义了税率，则使用供应商税率，如果在产品中也指定了税率，则使用产品的税率。





产品信息权限(先不用，待仔细考虑)
invku_sale_order_read,sale order read,sale.model_sale_order,base.group_jingying,1,0,0,0
invku_sale_order_line_read,sale order line read,sale.model_sale_order_line,base.group_jingying,1,0,0,0
invku_purchase_order_read,purchase order read,purchase.model_purchase_order,base.group_jingying,1,0,0,0
invku_purchase_order_line_read,purchase order line read,purchase.model_purchase_order_line,base.group_jingying,1,0,0,0
invku_product_all,product all,product.model_product_product,base.group_chanpin,1,1,1,1
invku_product_temp_all,product temp all,product.model_product_template,base.group_chanpin,1,1,1,1





## 第二部分 从财务管理开始



### 第4章收据和付款
### 第5章从发票到支付

#### 修改前缀
“设置→技术→序列与标识符→序号” 里面
INV改成发票，
BILL账单？
PO改成采购单
SO改成销售单

#### 财政状况
可以通过菜单“会计→配置→会计→财政状况”来设置科目和税的替换规则
这个替换规则是在客户/供应商信息中指定的，可以把全局默认规则替换
同时在建立销售报价单/采购单的“其它信息”时可以选择已经建立好的替换规则

#### 可以手动建立发票


#### 可以取消发票
默认情况下，Odoo 不允许你对已记账的发票进行取消。因为已创建了会计分录，理论上你不可以回退并删除它们。

在安装“account_cancel”模块后，通过菜单“会计→配置→会计→日记账”，选择相应的日记账种类，里面的高级设置，勾选相应的选项卡“允许取消分录”

如果符合以下两个条件，将允许取消发票：
1）会计分录尚未对帐或付款：如果已对账，那么你将不得不首先取消对帐。
2）会计期间或会计年度尚未被关闭（未月结）：如果它是关闭的，那么将没有任何修改的可能。
取消发票将自动修改相应的会计分录。
在发票窗体中点击“取消发票”按钮即可取消发票。

#### 合计和发票不一致原因
当你核准发票时，你需要检查不含税的发票行的小计和税两者之和与合计数是否和纸质发票一致。
合计数和你的供应商给你的纸质发票不一致的原因包括：
>>供应商计算错误
>>合计金额使用了不同的四舍五入方法

1. 这种情况下，你可以基于总计修改记录行上的值，或者修改表单左下侧的税金总额。它们都是可编辑的，因此你可以修改他们来调整总数。
>> 在草稿状态的账单和发票是可以编辑的，在采购一方编辑调整，在会计一方就会实时显示出来


2. 为了保证发票的合计数一致，有时你需要提高价格的小数点位数。使用菜单“设置→技术→数据库结构→小数准确性”，在列表里找到“ Product Price”，然后更改“位数”字段的值。

> 调整后就可以和供应商的纸质发票一致，然后再点击“验证”，这个时候，账单/发票变成打开状态，说明已经挂账完成。对于挂账完成的账单/发票不能编辑。
当合计一致，你就可以核准这张发票。Odoo会生成相应的会计分录，你可以使用发票上的科目字段和发票上每个记录行的科目字段来管理这些会计凭证。

#### 退款
你可以通过一张已存在的发票快速的生成一张退款单。选择一张“待支付”或“已付”状态的客户发票，然后点击发票窗体上部的“要求退款”按钮。

当创建了“草稿”状态的退款发票并不会影响日记账分录，一旦验证变成“打开”状态，就会产生日记账。观察日记账发现会和之前创建的原始发票的“借贷”关系相反，说明成功。


#### 付款
发票“记账”后，点击发票窗体上部的“登记付款”按钮，确认付款金额并选择付款方式，


#### 无用单据
无用的单据，如发票草稿，在Odoo中是不能删除的，你可以选择“取消”，对“取消”的单据后还可以“重置为草稿”，然后修改后重用。

#### 取消与撤销分录
日记账分成几类，银行，现金，客户，供应商等等。如果想取消日记账，需要把对应类别设置成可以取消，然后可以进行取消操作。
撤销操作，感觉是进行对冲操作使用的。对之前的日记账进行逆转，会新生成相反的条目。



#### 管理银行对帐单



#### 预付款管理
为了使用付款管理你需要安装采购付款管理“payment”模块，它是Odoo系统的可选模块，经查找状态为已安装。

在财务模块里面，如果预先收取了客户的预付款，或者把预付款提前给了供应商，可以先进行登记。在之后的交易中，在需要付款的时候，可以扣减这部分预付款。

#### 发票中的产品单价的小数精度问题
如果产品单价为4位小数精度，而发票里面每行单价只有2位小数单价。
手册中说addons/account/models/account_invoice.py里面的
price_unit = fields.Monetary(string='Unit Price', required=True)
修改一下，实际在10.0版本已经修改完成
price_unit = fields.Float(string='Unit Price', required=True, digits=dp.get_precision('Product Price'))


### 第6章财务分析
在打印报表页面：翻译有些问题，“有运动”应该翻译成“有发生额的”

#### 财务-报告-PDF报告-业务伙伴分类账：出PDF的时候报错
odoo10/addons/account/report/account_partner_ledger.py 38行左右的问题
已经在github 登记#24881，刚刚登记完，继续跟踪
解决方式是在 翻译里面，把简体中文的日期格式里面的汉字删除： %Y年%m月%d日


#### 业务伙伴信息
选择相应的客户/供应商后，进入form视图，右边有一个“动作”菜单，里面包含对该客户/供应商的相关操作。

在 Odoo 中，你可以基于应收帐款金额来搜索联系人。因此，搜索应收账款金额在 1 到 99999999 之间的业务伙伴时，会得到有欠款的业务伙伴列表。

#### 总帐和试算平衡
试算平衡可以快速的检查每个科目的余额

#### 税
可以通过会计->配置->会计->税，来设置税种
可以设置百分比，税科目和退款的税科目。


### 第7章设置公司的财务结构

#### 日记账（分录）
所有的会计凭证必须在会计分录中体现，因此你必须，至少为你的客户凭证创建一个销售分录，为你的供应商凭证创建一个采购分录，为你的现金和银行交易创建一个现金分录。

系统内置了如下分录
Customer Invoices：客户发票
Vendor Bills：供应商账单
Miscellaneous Operations：杂项操作
Exchange Difference：汇率差异
Bank：银行
Cash：现金
Stock Journal：库存分类账
每种类型有
每种分录是有
1. 类型，分录类型控制必须显示的字段和必须在分录中录入的财务数据。它定义了字段的规则和每个字段的属性。例如：在录入银行分录时账号必须显示，但是其它分录不需要该字段。

系统默认有 销售，采购，现金，银行，杂项，五种类型
2. 分录序列：这个规则决定了会计凭证的自动编号方法，也可以多个分录使用一个序号

3. 默认的借方科目和贷方科目（没有试验出来）
当你在分录中快速录入数据时，贷方和借方科目默认允许自动的生成相匹配的数据。例如，在银行分录中，你需要设置相对应的贷方和借方科目的银行帐号，这样你就不再需要为每一笔交易手动的输入相关的数据。

#### 数据录入时的控制和提示
在odoo中有两种方式控制分录，财务账户和组用户访问控制

在分录的高级选项->访问控制中可以选择“允许的科目类型”和“允许的科目”。如果留空，表示没有任何约束。这个功能对于限制可能发生的数据录入错误非常有用

举例：避免从银行账户A直接转到银行账户B，通过中间科目C处理交易

#### 会计期间及财年度
会计->配置->设置里面可以设置“财务年度的最后一天”
“锁定分录”里面有两个日期
非顾问的锁定日期：只有顾问才能更改此日期及之前的帐薄
锁定日期：任何人都不能更改此日期及之前的帐薄

在关闭财务年度之前有一系列的操作要处理，详见手册。


## 第三部分 管理公司的价值链
### 第8章销售管理
#### 销售订单填写
价单和销售订单通过菜单已经有所区分,你可以把销售订单理解为已经确认了的报价单（销售合同）。在确认报价单转为销售订单前，你录入的信息可被更改。 

主信息
价格表：确定报价单上使用的币种及每个产品单价。（为出现此字段，你需要通过“销售→配置→设置”，在“定价”一节的“标价”字段勾选“不同客户不同的价格”。） 
但是经过试验发现，价格表里只有币种信息，后来在产品的销售选项卡里面多了“定价”，就是价格表，指定的币种在这里作为名称出现，后续还需要继续试验。

辅助核算项：如果你通过任务来管理，就可以通过被选择的辅助核算项与此标价单关联。通过订单完成的销售额，将被指定给相关项目，这样就可以计算出项目的盈利情况。

其它信息选项卡中
客户参照：是客户自己的订单号。
贸易术语:用来说明这个销售贸易类型的一些提示信息. 
发货策略:决定这次销售，1）分批发货；2）整批发货. 
支付条款：表示顾客需要遵循的付款方法，例如： 30 天。 
财政状况: 用于税替换和科目替换
    通常，在产品窗体的“会计”选项卡中设定销售、采购该产品应缴纳的税，之后，在销售订单、采购单中，系统会自动采用产品上设定的税。但是，有时候，同一产品，针对不同类型客户，其计税方式不同。例如，某产品内销时要计消费税，但外销时不计消费税。那么，对于外销客户，应该采用不同于产品上设定的计税方法计税。
    当指定客户的替换规则后，系统会根据替换规则中的设定将产品中设定的税换成别的税计算税额。
    又如，在产品信息中设定销售、采购该产品记的收入、费用科目，之后，在销售订单、采购订单中，系统会自动采用产品上设定的科目生成发票。但是，有时候，同一产品，针对不同类型客户，其收入科目不同。例如，某产品内销时要记入主营业务收入-国内科目，但外销时要记入主营业务收入-国外科目。那么，对于外销客户，应该采用不同于产品上设定的科目。

    当指定客户的“财政状况”后，系统会根据替换规则中的设定将产品中设定的科目换成别的科目生成发票。

在报价单窗体的下部，你还可以为该报价单增加关注者，设置正在关注的事项或“新增内部备注”。

订单行选项卡
产品：选择将出售给客户的产品，点击文本框右侧的黑三角将显示在产品列表，你可以从中选择。

说明：系统会自动根据产品信息自动填写。
当选择了要销售给客户的产品后，Odoo 自动完成其它需要填写的字段，包括说明、数量、计量单位、税、单价、成本价、折扣等，所有这些信息都来源于产品窗体。当然，你可以修改它们。

计量单位：此字段默认不显示，你可以通过菜单“销售→配置→设置”，在“产品”一节的“计量单位”字段勾选“一些产品可使用不同的销售 / 采购单位 (高级）”。

成本价：指产品的成本价格，如果销售员销售的价格低于该价格，公司将承受损失。

折扣：可以通过“销售→配置→设置”，并在“报价单和销售”一节的“折扣”字段，勾选“允许在销售订单行上进行折扣”，就可达到以下效果：让折扣以标价的百分比的形式显示在销售订单上，或者，仅仅按照默认方式显示打完折扣后的价格。客户将在成本价旁边看到一个Ｘ%的折扣，该价格与标准价格不同。

在“合计”旁边点击“（更新）”，系统将自动计算小计和合计。

在销售订单中，选择的产品按照用户使用的语言显示，同时还可以按照客户的语言显示，打印报价单或销售订单时，它们将被翻译成客户使用的语言。在“产品”窗体中，进入编辑模式，点击“产品名称”右上角的图标，然后在翻译窗体中输入相关字段的翻译。

当录入所有产品后，你可以打印报价单，并把它发给客户。为此，从窗体中上部“打印”按钮下拉菜单中选择“报价单/订单”或直接点击“打印”按钮。Odoo 将用 PDF 格式显示该报价单，在打印之前你可以先看一下。

当客户确认了报价单后，你可以通过报价单窗体的“确认订单”按钮将此报价单转化为销售订单。

#### 客户要求日期，承诺日期和生效日期
在安装了模块sale_order_dates,以后，销售订单窗体的“其他信息”会出现三个字段

客户要求日期：客户要求的发运日期。当这个订单被确认后，发货的期望的日期将被基于这个日期和公司安全延迟计算。如果你希望这个发货越快越好，你就将此字段留空。这样期望的日期将使用默认的方法计算：基于产品提前期和公司的安全延迟。
承诺日期：产品必须确信被发运的日期，这是你答应客户的日期。
实际日期：创建第一个发货单的日期。

#### 包装
要使用包装功能，首先要通过菜单“仓库→配置→设置”，在“产品”一节的“包装方式”字段中勾选“管理每个产品的可用的包装选项”。
设置完成后，在产品窗体的“库存”选项卡的“包装”一节，通过“添加一个项目”来定义此产品可用的包装类型及其包装数量。比如快递袋装1个，纸箱装3个。
列表中的第一个项目作为默认包装类型使用。
通过菜单“仓库→配置→包裹类型”，你可以编辑或删除已有的包装。

一旦在销售订单的订单明细中中指定了包装类型，如果订购数量与包装类型的包装数量不匹配，Odoo 就弹出一个警告，订购数量必须是包装类型中定义的包装数量的整数倍。

不要混淆包装管理和多计量单位管理。计量单位指保存库存控制的最小可用单位；而包装指在流通过程中，为保护产品、方便储运、促进销售，依据不同情况而采用的容器、材料、辅助物及所进行的操作的总称。
即使效果一样，打印的文件也有区别。下面这两个操作在库存移动中有相同的效果，但是在打印销售订单和装箱单时会不同，如：
6 套 Odoo 应用手册，使用两个纸箱发货。
两箱 Odoo 应用手册，没有包装信息。
如果客户要订购 1 箱另加 1 套 Odoo 应用手册，销售员可以在销售订单上输入两个订单行,使用相同的产品而计量单位不同。

有时候更喜欢使用物料清单来管理，把包装放到物料清单里面，而不是通过建立包裹的方法。

#### 警告
10.0中没有发现“warning”警告模块，在业务伙伴页面也没有发现“警告”选项卡，在产品页面的备注里有一些说明，也不是警告。经测试这些说明会分别出现在采购和销售单的说明中。

#### 发货方式、开票控制和月底对账
发货方式：如果销售订单选择了“分批发货”，一旦销售的产品有库存，发货单就会显示“部分可用”。库管员可以按照实际可用的库存来部分发货，以后当仓库中有可用的库存时，库管员可再次发货。
如果运送方式是“整批发货”，发货单的状态将显示“等待可用”，直到仓库中的可用库存满足销售订单的产品数量时，才显示“可用”。

在产品窗体的“一般信息”选项卡中你可以设置产品的开票策略:控制采购账单，分两种：收到数量和订购数量

月底对账：对于长期客户，在月底一般都要由销售部门和客户进行对账。
通过菜单“销售→销售→销售订单”，你可以根据客户、日期、发票装态等，对显示的销售订单进行筛选。然后选择要导出的销售订单，
最后从动作下拉菜单中选择“导出”。

#### 多个订单合并开票
通过菜单“销售→销售→销售订单”，选择要开票的多个订单，从“动作”菜单中选择“基于订单开票”
前提是产品已经交货，就是从库房发出，否则会提示没有订单项。

#### 运输成本管理
为了在 Odoo 中管理发货成本，需要安装“delivery（运输成本）”模块。
这个模块能够使你管理：
>>与你合作的物流公司，
>>不同的运输方法，
>>每次发货的成本计算及开票，
>>运输方法和关税。
Odoo 会自动根据创建好的运输方式新建一个可采购的产品与之对应，具体使用方法，详见手册。

#### 按照发货单开票
要根据运送产品的实际数量开销售发票，需要将销售订单窗体的“其它信息”选项卡的“生成发票”字段中设置为“基于发货单”。不要在销售订单中增加运费，你需要在基于已交付的产品数量生成发票后，再把运费加到发票中。
然后，你可以确认订单，并确认发货。
当按照已设置的发送方式生成发票，Odoo 自动在每张销售发票上增加相关的运费，运费的计算基于实际产生的运费。


#### 跟踪利润率
对每家企业而言，控制好利润率非常重要。即使你有着良好的销售额，如果利润率不够高，也将无法保证公司的盈利能力。Odoo 提供很多方式监控利润率。主要的有：
>>销售订单的利润率，首先要安装“sale_margin”模块，在报价单或销售订单的产品明细下方会显示“毛利”的金额。
毛利的计算公式：销售数量×(客户的销售价格-产品成本价格)。
>>产品利润率，要跟踪产品利润率，首先要安装“product_margin”模块，使用菜单“会计→报告→管理→产品毛利”可对产品利润率进行分析。Odoo提示输入时间区段和要分析的发票状态。Odoo会默认提供过去12个月内待处理和支付的利润率分析。

>>项目利润率
>>使用价格表





### 第9章采购管理
### 第10章多公司环境

## 第四部分 管理仓库并实现生产
### 第11章仓库管理系统（WMS）
### 第12章生产

## 第五部分 推动销售业绩增长
### 第13章通过CRM跟踪线索和商机
### 第14章联系人管理
### 第15章CMS内容管理系统

## 第六部分 有效的运营管理
### 第16章辅助核算项和预算管理
### 第17章领导和激励你的员工
### 第18章项目管理
### 第19章集成文档管理

## 第七部分 高效的沟通管理
### 第20章会话交流功能
### 第21章频道
### 第22章电子邮件
### 第23章活动管理
### 第24章车辆管理

## 第八部分 系统管理和实施
### 第25章设置和管理
### 第26章Odoo的实施
### 第27章Odoo的简单二次开发
### 第28章你并不孤单


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
