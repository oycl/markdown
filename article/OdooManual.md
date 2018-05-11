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
财务中即使顾问也没有权限增加客户和供应商信息。

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
公司决定让哪个用户有这个权利就把这个用户添加到该群组中。



产品信息权限
invku_sale_order_read,sale order read,sale.model_sale_order,base.group_jingying,1,0,0,0
invku_sale_order_line_read,sale order line read,sale.model_sale_order_line,base.group_jingying,1,0,0,0
invku_purchase_order_read,purchase order read,purchase.model_purchase_order,base.group_jingying,1,0,0,0
invku_purchase_order_line_read,purchase order line read,purchase.model_purchase_order_line,base.group_jingying,1,0,0,0
invku_product_all,product all,product.model_product_product,base.group_chanpin,1,1,1,1
invku_product_temp_all,product temp all,product.model_product_template,base.group_chanpin,1,1,1,1

## 第二部分 从财务管理开始



### 第4章收据和付款
### 第5章从发票到支付
### 第6章财务分析
### 第7章设置公司的财务结构


## 第三部分 管理公司的价值链
### 第8章销售管理
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
