# Odoo 表格解析

## 从删除demo数据得到的表关系
1，从采购开始,可以考虑加上一个合同编号

采购-》采购订单：purchase_order表
外键有下面几个
res_company (id)             company_id  指向自己公司，购买方
res_currency (id)            currency_id 使用的货币
res_partner (id)             dest_address_id 解释为Drop Ship Address，demo中为空，猜想和多地址收货有关
account_fiscal_position (id) fiscal_position_id 解释Fiscal Position，demo中为空，翻译过来叫财政状况，在供应商/客户处可以选择，在订单处可以选择。在会计处可以设置：设置用来替代的税和科目，
这里多说两句：
如果在供应商/客户处选择了财务状况，Odoo将把这一特定财务状况分配给客户记录的任何订单/发票
如果在销售订单或发票中手动设置了财务状况，则其只会应用到这一个文件，而不会应用到同一客户的未来订单/发票
在会计科目表中也设置了默认税，留个疑问。
procurement_group (id)       group_id 解释为-- Procurement Group，任何采购单和销售单都会在其中产生记录
stock_incoterms (id)         incoterm_id 解释为-- Incoterm贸易术语，专门有一个表存放，demo中为空
res_partner (id)             partner_id 解释为-- Vendor ，这个是真正的供应商
account_payment_term (id)    payment_term_id，付款条款，对应的表应该给财务管理，只有提示作用，并无特殊功能。demo中有3，4和空，3代表30净天，4代表下月的最后
stock_picking_type (id)      picking_type_id，解释为Deliver To，就是来货的时候入哪个仓库，在demo中为21，继续在stock_picking_type里追踪21，这一条代表收货，采购单对应到收货上了



配置-》作业类型：stock_picking_type表
目前知道对于每个仓库都会建立收货，打包，捡货，发货，内部调拨等类型，以21收货为例
stock_location (id)     default_location_dest_id默认目的地89为WH/Stock主库位
stock_location (id)     default_location_src_id默认源位置，为空，其实是供应商库位，但是没有给每个供应商建立库位，统一使用一个库位，这里为空。
stock_picking_type (id) return_picking_type_id 返回操作，如果是发货就会指定收货，反之亦然。
ir_sequence (id)        sequence_id，根据公司指定了前缀WH/IN/，WH/PACK/，WH/PICK/等等
stock_warehouse (id)    warehouse_id，指仓库，5为WH,6为CHIC



做一个确认订单的操作会先在Stock_picking表产生记录，然后stock_move表，stock_move表会引用stock_packing表
A stock picking represents an operation, let's say, you taking some goods from location A to location B. You move 10 item A and 10 item B. 
A stock move represents the action of moving 10 items A from location A to location B. Another stock move is the action of moving 10 item B from location A to location B.

A stock picking may contain several stock moves. The stock picking is the moving operation. Stock moves represent individual stock movement.



作业-》所有调拨：Stock_picking表
在库存作业里面有19条记录，对应这个表
stock_picking (id)  backorder_id  指向自己，在欠单的情况下使用
res_company (id)    company_id    demo中，都指向一拖
procurement_group (id) group_id   任何采购单和销售单都会在其中产生记录，包含采购/销售单号和供应商/客户信息
stock_location (id)   location_dest_id  目的地，只要是发货给客户的其值都是Partner Locations/Customers，同理收货的也应该是Partner Locations/Vendors，
stock_location (id)   location_id       源地址，代表从自己的哪个仓库发出
这两个地址都在stock_location表里面，如果是多公司，每个公司的库位都会在里面，注意这个表使用location_id这个字段指定库位的上级，而顶级库位只有三个Physical Locations，Partner Locations，Virtual Locations
res_partner (id)      owner_id  解释-- Owner，demo数据为空
res_partner (id)      partner_id 解释-- Partner，就是我们的客户或者供应商等
stock_picking_type (id) picking_type_id  我们称之为捡货类型，非空，参照上面



报告-》库存移动：stock_move表
res_company (id)          company_id一拖
procurement_group (id)    group_id按采购单/销售单号分组
stock_inventory (id)      inventory_id没填
stock_location (id)       location_dest_id目的地83代表Partner Locations/Customers
stock_location (id)       location_id源地址89代表WH主库存Stock，82代表供应商
stock_move (id)           move_dest_id，采购单和销售单刚建立时为空，实际指向自己表的一条记录
stock_move (id)           origin_returned_move_id，采购单和销售单刚建立时为空，实际指向自己表的一条记录
res_partner (id)          partner_id，销售单为8代表客户，采购单为空
stock_picking (id)        picking_id，指向stock_picking里的一个具体操作，一个操作对多个移动
stock_picking_type (id)   picking_type_id，操作类型，收货和发货等
procurement_order (id)    procurement_id，销售单关联procurement_order，采购单为空
product_product (id)      product_id，产品
product_packaging (id)    product_packaging，目前为空
product_uom (id)          product_uom，单位
purchase_order_line (id)  purchase_line_id，采购订单项表
stock_location_path (id)  push_rule_id，空不知道作用
stock_production_lot (id) restrict_lot_id，批次相关，目前无用
res_partner (id)          restrict_partner_id，空
procurement_rule (id)     rule_id，补货规则，销售单有17代表WH: Stock -> Customers，几个仓库都建有补货规则在表procurement_rule
stock_move (id)           split_from，空
stock_warehouse (id)      warehouse_id，5代表WH，一拖仓库


库存管理-》库存调整：stock_inventory表及stock_inventory_line表



1，首先删除stock_inventory_line表和stock_inventory表，这两个表在“库存管理-》库存调整”菜单里面可以直接删除

2，报告-》库存移动：所有的库存移动，stock_move表，但是提示只能删除草稿，去数据库删除
DELETE FROM public.stock_move;执行成功

3，作业-》所有调拨：Stock_picking表，发现不能删除stock_pack_operation里面有指向这个表的引用
先使用DELETE FROM public.stock_pack_operation;成功
然后到作业-》所有调拨：里面去删除所有记录

在这个时候，库存仪表板下面的所有待办事项全部变成0，


4，库存管理-》重订货规则：stock_warehouse_orderpoint表
里面有四条记录，在web页面就可以全部删除

5，stock_route_warehouse表，删除另外两个公司共6条数据，只保留一拖warehouse_id
使用DELETE FROM public.stock_route_warehouse WHERE warehouse_id != 5;
stock_location_route (id)    route_id
stock_warehouse (id)         warehouse_id
继续顺藤摸瓜试验，stock_location_route只保留一拖名称和BUY MTO
DELETE FROM public.stock_location_route WHERE id between 20 AND 25;

6，stock_route_product表，这个表对应产品页面里面库存里面的路线，如果点选购买，则该产品对应route_id为BUY，
product_template (id)      product_id
stock_location_route (id)  route_id
全部删除应该没有关系 DELETE FROM public.stock_route_product;

7，报告-》库存计价：stock_quant表
全部删除：DELETE FROM public.stock_quant;

8，stock_production_lot
删除：DELETE FROM public.stock_production_lot;


9，purchase_order(purchase_order_line)，和sale_order(sale_order_line)，如果删除收货单，采购单也应该能删除
DELETE FROM public.sale_order_line;
DELETE FROM public.sale_order;
DELETE FROM public.procurement_order;
DELETE FROM public.procurement_group;
DELETE FROM public.purchase_order_line;
DELETE FROM public.purchase_order;

10，stock_picking_type和一拖无关的移动
DELETE FROM public.stock_picking_type where warehouse_id between 6 and 7;
失败，在procurement_rule中有引用
继续处理procurement_rule
DELETE FROM public.procurement_rule WHERE id between 21 and 28;
DELETE FROM public.procurement_rule WHERE id=30;
回头继续
DELETE FROM public.stock_picking_type where warehouse_id between 6 and 7;
成功


11，在配置-》仓库：stock_warehouse里面，可以删除Chicago Warehouse下面那个子仓库和Chicago Warehouse

12，但是还没有处理stock_location，如果不放心，把active设置成false就行了




## 一，财务
欢迎
这条向导将会帮你开启Odoo的会计，一旦你完成，你将会获益于：

减少的数据录入: 无需手动创建发票, 登记收付款, 和将收付款情况更新到款项追踪.
Reduced data entry: no need to manually create invoices, register bank statements, and send payment follow-ups.
清理销售发票:方便创建，美观功能齐全的发票。
Clean customer invoices: easy to create, beautiful and full featured invoices.
文档自动发送: 自动将发票通过电子邮件发送
Automated documents sending: automatically send your invoices by email or snail mail.
自动付款对账: 创建发票时自动生成未清款项.
On-the-fly payment reconciliation: automatic suggestions of outstanding payments when creating invoices.
银行界面: 与银行实时同步及导入银行对账单
Banking interface: with live bank feed synchronization and bank statement import.

5)要设置的话，你需要以下信息:

公司的法定名称，税号，地址，和logo
Your company's legal name, tax ID, address, and logo.
一个银行对账银行对应你持有的一个银行账号
One bank statement for each bank account you hold.
客户和供应商的付款条款列表。
A list of your customer and supplier payment terms.
通用税和税率的列表
A list of common taxes and their rates.
你的试算表（科目清单以及余额）。
Your Trial Balance (list of accounts and their balances).
未完成发票，付款，以及未存款的资金
Your outstanding invoices, payments, and undeposited funds.

在设置的过程中，如果设置“你的银行账户”会在科目表里面复制一份同样科目

### 会计科目表相关
#### 应用安装
如果首先安装“会计”应用，则该应用会自动找到“中国会计科目表”（技术名称l10n_cn）并安装。看了一下其源码，只加入了一个中国的省份数据和类型：不显示在报表上
继续查看数据库，account_account内无数据，account_account_template内无数据，account_chart_template内无数据，account_account_type里面有18行数据（包含上面的“不显示在报表上”）
说明这些类型是系统预置的，和具体装哪个国家的会计科目无关。

继续看应用“中国小企业会计科目表”l10n_cn_small_business,
其在account.chart.template里面添加了一条数据
```xml
    <record id="l10n_chart_china_small_business" model="account.chart.template">
            <field name="name">小企业会计科目表（财会[2011]17号《小企业会计准则》）</field>
            <field name="code_digits" eval="4"/>
            <field name="currency_id" ref="base.CNY"/>
            <field name="cash_account_code_prefix">1001</field>
            <field name="bank_account_code_prefix">1002</field>
            <field name="transfer_account_id" ref="small_business_chart1012"/>
			account_account_template里的第一条记录“其他货币资金”，这个值就是在“会计”设置里“银行间转账科目”
    </record>
```
然后在account.account.template里面添加64个会计科目，这些科目的chart_template_id字段关联到表account_chart_template的这个“id="l10n_chart_china_small_business"”


最后又修改了属性，添加了税
```xml
     <!-- Chart template -->
        <record id="l10n_chart_china_small_business" model="account.chart.template">
            <field name="property_account_receivable_id" ref="small_business_chart1122"/>应收账款
            <field name="property_account_payable_id" ref="small_business_chart2202"/>应付账款
            <field name="property_account_expense_categ_id" ref="small_business_chart1401"/>材料采购
            <field name="property_account_income_categ_id" ref="small_business_chart5001"/>主营业务收入
            <field name="income_currency_exchange_account_id" ref="small_business_chart5301"/>营业外收入
            <field name="expense_currency_exchange_account_id" ref="small_business_chart5712"/>汇总损失
        </record>

        <record id="l10n_chart_china_small_business_value" model="ir.values">
            <field name="name">小企业会计科目表（财会[2011]17号《小企业会计准则》）</field>
            <field name="model">account.chart.template</field>
            <field name="key">default</field>
            <field name="res_id" ref="l10n_chart_china_small_business"/>
            <field name="value" ref="base.CNY"/>
        </record>

        <!-- Account Tax Tags-->
        <record id="tax_tag1" model="account.account.tag">
            <field name="name">增值税17％销项税</field>
        </record>
        <record id="tax_tag2" model="account.account.tag">
            <field name="name">增值税17%进项税</field>
        </record>

        <!-- 增值税 -->
        <record id="vats_small_business" model="account.tax.template">
            <field name="chart_template_id" ref="l10n_chart_china_small_business"/>
            <field name="name">增值税17％销项税 - 中国小企业会计科目表</field>
            <field name="description">增值税17％销项税</field>
            <field eval="17" name="amount"/>
            <field name="amount_type">percent</field>
            <field name="refund_account_id" ref="small_business_chart2221"/>
            <field name="account_id" ref="small_business_chart2221"/>
            <field name="type_tax_use">sale</field>
            <field name="tag_ids" eval="[(6,0,[ref('tax_tag1')])]"/>
        </record>

        <record id="vatp_small_business" model="account.tax.template">
            <field name="chart_template_id" ref="l10n_chart_china_small_business"/>
            <field name="name">增值税17%进项税 - 中国小企业会计科目表</field>
            <field name="description">增值税17%进项税</field>
            <field eval="17" name="amount"/>
            <field name="amount_type">percent</field>
            <field name="refund_account_id" ref="small_business_chart2221"/>
            <field name="account_id" ref="small_business_chart2221"/>
            <field name="type_tax_use">purchase</field>
            <field name="tag_ids" eval="[(6,0,[ref('tax_tag2')])]"/>
        </record>
```


我们直接把excel文件导入系统观察，所有数据进入account_account，account_account_template里面没有数据，account_chart_template里面也没有数据。为了使会计仪表板显示内容，我们使用改写应用的方法，先把导入account_account这部分数据删除，删除还好在数据里使用DELETE FROM public.account_account;。如果供应商客户或者公司引用其中的数据，删除时就会变得很麻烦。

一直没找到account_account_template里面的数据是如何复制到account_account里面的，留个疑问。

#### 改写步骤
1，把l10n_cn_small_business文件夹复制一份到custom-addons文件夹里面，改small_business为yto，不管文件夹还是扩展ID，原则是把small_business都改成yto
2，改写__manifest__.py，修改一些描述不是很重要，主要是data域里面把small_business改成yto
3，改写data/l10n_cn_yto_chart_data.xml
把其它1012其他货币资金前（36行前）的small_business都用yto代替
把1012用1009其他货币资金代替，在一拖会计科目中是1009
注意：cash_account_code_prefix和bank_account_code_prefix如果指定代码会自动生成两个科目（使用100，就会自动生成1001Cash和1002Bank）
也就是说在下面的会计科目主体中不用再出现“现金”和“银行存款”两个科目的代码

编写会计科目的主体：
01拿到具体的会计科目表01ChartOfAccounts.xls
02改写最后的样子02ChartOfAccountsTest.csv
注意几点：
id就是一个会计科目的扩展id，注意如果出现点.分隔符，就代表点前面是一个模块，如果没有这个意思就不能使用点，可以使用下划线_代替（如果不方便可以单拿一列出来在VIM中批量替换）。这个规则全ODOO的扩展标识符都适用。
reconcile里面大小写敏感，使用False和True，不能使用FALSE和TRUE，应收和应付一定为True
chart_template_id里指向l10n_chart_china_yto，下面会说明
user_type_id_exid指的是科目类型具体参照ChartTypeExternalIdentifier.xls


03把02ChartOfAccountsTest.csv用记事本打开另存为03ChartOfAccountsTestUtf8.csv，注意选编码为UTF-8
04在https://www.freeformatter.com/csv-to-xml-converter.html做转换
选择03ChartOfAccountsTestUtf8.csv并使用下面的模板
       <record id="##1##" model="##2##">
            <field name="code">##3##</field>
            <field eval="##7##" name="reconcile"/>
            <field name="name">##4##</field>
            <field name="user_type_id" ref="##6##"/>
            <field name="chart_template_id" ref="##8##"/>
        </record>
然后把转换后的xml格式文件复制出来04FinalXML.txt，替换原会计科目表的主体部分

最后把会计科目表的一些默认科目改写完，把税改写完


4，改写data/account_chart_template_data.yml
只有一个small_business改成yto，变成l10n_chart_china_yto，注意这个ID代表我们要安装的这个会计科目表，在表account.chart.template里面存储，还指定了几个默认的会计科目（在上面科目表的尾部指定）


#### 分析


模型层
```python
class AccountAccount(models.Model):
    _name = "account.account"
    _description = "Account"
    _order = "code"

    @api.multi
    @api.constrains('internal_type', 'reconcile')
    def _check_reconcile(self):
        for account in self:
            if account.internal_type in ('receivable', 'payable') and account.reconcile == False:
                raise ValidationError(_('You cannot have a receivable/payable account that is not reconciliable. (account code: %s)') % account.code)
    这句翻译过来就是如果账户类型是“应付”和“应收”，那么可调节选项（字段reconcile）不能是false，就是要可以调节
    name = fields.Char(required=True, index=True)
    currency_id = fields.Many2one('res.currency', string='Account Currency',
        help="Forces all moves for this account to have this account currency.")
    code = fields.Char(size=64, required=True, index=True)
    deprecated = fields.Boolean(index=True, default=False)
    user_type_id = fields.Many2one('account.account.type', string='Type', required=True, oldname="user_type", 
        help="Account Type is used for information purpose, to generate country-specific legal reports, and set the rules to close a fiscal year and generate opening entries.")
    internal_type = fields.Selection(related='user_type_id.type', string="Internal Type", store=True, readonly=True)
	internal_type内部类型：使用了关联字段，首先这个字段是本模型中的一个字段user_type_id，而且这个字段是一个Many2one类型的，这样在实际的页面输入数据的时候，如果输入了user_type_id的值，那么在其对应的account.account.type模型中取到对应的type字段，给本字段赋默认值。

    #has_unreconciled_entries = fields.Boolean(compute='_compute_has_unreconciled_entries',
    #    help="The account has at least one unreconciled debit and credit since last time the invoices & payments matching was performed.")
    last_time_entries_checked = fields.Datetime(string='Latest Invoices & Payments Matching Date', readonly=True, copy=False,
        help='Last time the invoices & payments matching was performed on this account. It is set either if there\'s not at least '\
        'an unreconciled debit and an unreconciled credit Or if you click the "Done" button.')
    reconcile = fields.Boolean(string='Allow Reconciliation', default=False,
        help="Check this box if this account allows invoices & payments matching of journal items.")
    tax_ids = fields.Many2many('account.tax', 'account_account_tax_default_rel',
        'account_id', 'tax_id', string='Default Taxes')
    note = fields.Text('Internal Notes')
    company_id = fields.Many2one('res.company', string='Company', required=True,
        default=lambda self: self.env['res.company']._company_default_get('account.account'))
    tag_ids = fields.Many2many('account.account.tag', 'account_account_account_tag', string='Tags', help="Optional tags you may want to assign for custom reporting")

    _sql_constraints = [
        ('code_company_uniq', 'unique (code,company_id)', 'The code of the account must be unique per company !')
    ]



class AccountAccountType(models.Model):
    _name = "account.account.type"
    _description = "Account Type"

    name = fields.Char(string='Account Type', required=True, translate=True)
    include_initial_balance = fields.Boolean(string="Bring Accounts Balance Forward", help="Used in reports to know if we should consider journal items from the beginning of time instead of from the fiscal year only. Account types that should be reset to zero at each new fiscal year (like expenses, revenue..) should not have this option set.")
    type = fields.Selection([
        ('other', 'Regular'),
        ('receivable', 'Receivable'),
        ('payable', 'Payable'),
        ('liquidity', 'Liquidity'),
    ], required=True, default='other',
        help="The 'Internal Type' is used for features available on "\
        "different types of accounts: liquidity type is for cash or bank accounts"\
        ", payable/receivable is for vendor/customer accounts.")
	这里就可以看出，内部类型一共有四种类型，默认是other
    note = fields.Text(string='Description')

```



表示层
Type
Account Type is used for information purpose, to generate country-specific legal reports, and set the rules to close a fiscal year and generate opening entries.
类型
“帐户类型”用于信息目的，用于生成特定于国家/地区的法律报告，并将规则设置为关闭一个会计年度并生成开始条目。（个人）


Tags	
Optional tags you may want to assign for custom reporting
标签
您可能想要为自定义报告指定的可选标记

Account Currency	
Forces all moves for this account to have this account currency.
账户币种
强制此帐户的所有操作都拥有此帐户币种。

Internal Type	
The 'Internal Type' is used for features available on different types of accounts: liquidity type is for cash or bank accounts, payable/receivable is for vendor/customer accounts.
内部类型
“内部类型”用于不同类型账户可用的功能：流动性类型用于现金或银行账户，应付账款/应收账款用于供应商/客户账户。
****
类型和内部类型对照表
```xml
类型（英文）             |类型（中文）|内部类型
"Receivable"             |应收        |"receivable"
"Payable"                |应付        |"payable"
"Bank and Cash"          |银行和现金  |"liquidity"
"Credit Card"            |信用卡      |"liquidity"
"Current Assets"         |流动资产    |"other"
"Non-current Assets"     |非流动资产  |
"Prepayments"            |预付账款    |
"Fixed Assets"           |固定资产    |
"Current Liabilities"    |流动负债
"Non-current Liabilities"|非流动负债
"Equity"                 |权益
"Current Year Earnings"  |当年盈利
"Other Income"           |其他收入
"Income"                 |收入
"Depreciation"           |折旧
"Expenses"               |费用
"Cost of Revenue"        |业务成本
"不显示在报表上"

内部类型只把类型中的应收，应付，银行和现金，信用卡拿出来，其它类型通通归为“other”

```





Allow Reconciliation	
Check this box if this account allows invoices & payments matching of journal items.
允许调节
如果此帐户是允许与发票和付款相匹配的日记账条目，请选中此框


表示层改变数据，验证和模型层对应。


1.account_account_template里面存放了会计科目表
code 代码
name 名称
user_type_id 类型 关联account_account_type
chart_template_id 模板 关联account_chart_template，这里面只有一行
reconcile boolean 

2. 类型
。


## 二，物料


#### 分析
模型层
```python
class ProductTemplate(models.Model):
    _name = "product.template"
    _inherit = ['mail.thread']
    _description = "Product Template"
    _order = "name"

    def _get_default_category_id(self):
        if self._context.get('categ_id') or self._context.get('default_categ_id'):
            return self._context.get('categ_id') or self._context.get('default_categ_id')
        category = self.env.ref('product.product_category_all', raise_if_not_found=False)
        return category and category.type == 'normal' and category.id or False

    def _get_default_uom_id(self):
        return self.env["product.uom"].search([], limit=1, order='id').id

    name = fields.Char('Name', index=True, required=True, translate=True)
    sequence = fields.Integer('Sequence', default=1, help='Gives the sequence order when displaying a product list')
    description = fields.Text(
        'Description', translate=True,
        help="A precise description of the Product, used only for internal information purposes.")
    description_purchase = fields.Text(
        'Purchase Description', translate=True,
        help="A description of the Product that you want to communicate to your vendors. "
             "This description will be copied to every Purchase Order, Receipt and Vendor Bill/Refund.")
    description_sale = fields.Text(
        'Sale Description', translate=True,
        help="A description of the Product that you want to communicate to your customers. "
             "This description will be copied to every Sale Order, Delivery Order and Customer Invoice/Refund")
    type = fields.Selection([
        ('consu', _('Consumable')),
        ('service', _('Service'))], string='Product Type', default='consu', required=True,
        help='A stockable product is a product for which you manage stock. The "Inventory" app has to be installed.\n'
             'A consumable product, on the other hand, is a product for which stock is not managed.\n'
             'A service is a non-material product you provide.\n'
             'A digital content is a non-material product you sell online. The files attached to the products are the one that are sold on '
             'the e-commerce such as e-books, music, pictures,... The "Digital Product" module has to be installed.')
    rental = fields.Boolean('Can be Rent')
    categ_id = fields.Many2one(
        'product.category', 'Internal Category',
        change_default=True, default=_get_default_category_id, domain="[('type','=','normal')]",
        required=True, help="Select category for the current product")

    currency_id = fields.Many2one(
        'res.currency', 'Currency', compute='_compute_currency_id')

    # price fields
    price = fields.Float(
        'Price', compute='_compute_template_price', inverse='_set_template_price',
        digits=dp.get_precision('Product Price'))
    list_price = fields.Float(
        'Sale Price', default=1.0,
        digits=dp.get_precision('Product Price'),
        help="Base price to compute the customer price. Sometimes called the catalog price.")
    lst_price = fields.Float(
        'Public Price', related='list_price',
        digits=dp.get_precision('Product Price'))
    standard_price = fields.Float(
        'Cost', compute='_compute_standard_price',
        inverse='_set_standard_price', search='_search_standard_price',
        digits=dp.get_precision('Product Price'), groups="base.group_user",
        help="Cost of the product, in the default unit of measure of the product.")

    volume = fields.Float(
        'Volume', compute='_compute_volume', inverse='_set_volume',
        help="The volume in m3.", store=True)
    weight = fields.Float(
        'Weight', compute='_compute_weight', digits=dp.get_precision('Stock Weight'),
        inverse='_set_weight', store=True,
        help="The weight of the contents in Kg, not including any packaging, etc.")

    warranty = fields.Float('Warranty')
    sale_ok = fields.Boolean(
        'Can be Sold', default=True,
        help="Specify if the product can be selected in a sales order line.")
    purchase_ok = fields.Boolean('Can be Purchased', default=True)
    pricelist_id = fields.Many2one(
        'product.pricelist', 'Pricelist', store=False,
        help='Technical field. Used for searching on pricelists, not stored in database.')
    uom_id = fields.Many2one(
        'product.uom', 'Unit of Measure',
        default=_get_default_uom_id, required=True,
        help="Default Unit of Measure used for all stock operation.")
    uom_po_id = fields.Many2one(
        'product.uom', 'Purchase Unit of Measure',
        default=_get_default_uom_id, required=True,
        help="Default Unit of Measure used for purchase orders. It must be in the same category than the default unit of measure.")
    company_id = fields.Many2one(
        'res.company', 'Company',
        default=lambda self: self.env['res.company']._company_default_get('product.template'), index=1)
    packaging_ids = fields.One2many(
        'product.packaging', 'product_tmpl_id', 'Logistical Units',
        help="Gives the different ways to package the same product. This has no impact on "
             "the picking order and is mainly used if you use the EDI module.")
    seller_ids = fields.One2many('product.supplierinfo', 'product_tmpl_id', 'Vendors')

    active = fields.Boolean('Active', default=True, help="If unchecked, it will allow you to hide the product without removing it.")
    color = fields.Integer('Color Index')

    attribute_line_ids = fields.One2many('product.attribute.line', 'product_tmpl_id', 'Product Attributes')
    product_variant_ids = fields.One2many('product.product', 'product_tmpl_id', 'Products', required=True)
    # performance: product_variant_id provides prefetching on the first product variant only
    product_variant_id = fields.Many2one('product.product', 'Product', compute='_compute_product_variant_id')

    product_variant_count = fields.Integer(
        '# Product Variants', compute='_compute_product_variant_count')

    # related to display product product information if is_product_variant
    barcode = fields.Char('Barcode', oldname='ean13', related='product_variant_ids.barcode')
    default_code = fields.Char(
        'Internal Reference', compute='_compute_default_code',
        inverse='_set_default_code', store=True)

    item_ids = fields.One2many('product.pricelist.item', 'product_tmpl_id', 'Pricelist Items')

    # image: all image fields are base64 encoded and PIL-supported
    image = fields.Binary(
        "Image", attachment=True,
        help="This field holds the image used as image for the product, limited to 1024x1024px.")
    image_medium = fields.Binary(
        "Medium-sized image", attachment=True,
        help="Medium-sized image of the product. It is automatically "
             "resized as a 128x128px image, with aspect ratio preserved, "
             "only when the image exceeds one of those sizes. Use this field in form views or some kanban views.")
    image_small = fields.Binary(
        "Small-sized image", attachment=True,
        help="Small-sized image of the product. It is automatically "
             "resized as a 64x64px image, with aspect ratio preserved. "
             "Use this field anywhere a small image is required.")




class ProductProduct(models.Model):
    _name = "product.product"
    _description = "Product"
    _inherits = {'product.template': 'product_tmpl_id'}
	委托继承，父模型是product.template
    _inherit = ['mail.thread']
    _order = 'default_code, name, id'

    price = fields.Float(
        'Price', compute='_compute_product_price',
        digits=dp.get_precision('Product Price'), inverse='_set_product_price')
    price_extra = fields.Float(
        'Variant Price Extra', compute='_compute_product_price_extra',
        digits=dp.get_precision('Product Price'),
        help="This is the sum of the extra price of all attributes")
    lst_price = fields.Float(
        'Sale Price', compute='_compute_product_lst_price',
        digits=dp.get_precision('Product Price'), inverse='_set_product_lst_price',
        help="The sale price is managed from the product template. Click on the 'Variant Prices' button to set the extra attribute prices.")

    default_code = fields.Char('Internal Reference', index=True)
    code = fields.Char('Internal Reference', compute='_compute_product_code')
    partner_ref = fields.Char('Customer Ref', compute='_compute_partner_ref')

    active = fields.Boolean(
        'Active', default=True,
        help="If unchecked, it will allow you to hide the product without removing it.")
	
    product_tmpl_id = fields.Many2one(
        'product.template', 'Product Template',
        auto_join=True, index=True, ondelete="cascade", required=True)
	通过这个ID来连接product.template

    barcode = fields.Char(
        'Barcode', copy=False, oldname='ean13',
        help="International Article Number used for product identification.")
    attribute_value_ids = fields.Many2many(
        'product.attribute.value', string='Attributes', ondelete='restrict')
    # image: all image fields are base64 encoded and PIL-supported
    image_variant = fields.Binary(
        "Variant Image", attachment=True,
        help="This field holds the image used as image for the product variant, limited to 1024x1024px.")
    image = fields.Binary(
        "Big-sized image", compute='_compute_images', inverse='_set_image',
        help="Image of the product variant (Big-sized image of product template if false). It is automatically "
             "resized as a 1024x1024px image, with aspect ratio preserved.")
    image_small = fields.Binary(
        "Small-sized image", compute='_compute_images', inverse='_set_image_small',
        help="Image of the product variant (Small-sized image of product template if false).")
    image_medium = fields.Binary(
        "Medium-sized image", compute='_compute_images', inverse='_set_image_medium',
        help="Image of the product variant (Medium-sized image of product template if false).")

    standard_price = fields.Float(
        'Cost', company_dependent=True,
        digits=dp.get_precision('Product Price'),
        groups="base.group_user",
        help="Cost of the product template used for standard stock valuation in accounting and used as a base price on purchase orders. "
             "Expressed in the default unit of measure of the product.")
    volume = fields.Float('Volume', help="The volume in m3.")
    weight = fields.Float(
        'Weight', digits=dp.get_precision('Stock Weight'),
        help="The weight of the contents in Kg, not including any packaging, etc.")

    pricelist_item_ids = fields.Many2many(
        'product.pricelist.item', 'Pricelist Items', compute='_get_pricelist_items')

    _sql_constraints = [
        ('barcode_uniq', 'unique(barcode)', _("A barcode can only be assigned to one product !")),
    ]


```





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
