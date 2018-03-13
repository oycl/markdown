# Odoo 表格解析

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
然后在account.account.template里面添加66个会计科目，这些科目的chart_template_id字段关联到表account_chart_template的这个“id="l10n_chart_china_small_business"”


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


我们直接把execl文件导入系统观察，所有数据进入account_account，account_account_template里面没有数据，account_chart_template里面也没有数据


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
