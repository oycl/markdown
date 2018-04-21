# 库房系统安装配置

## 一，模块安装
### 以框架+ultra+现用用户为基础，进一步安装各个模块的方法（不打算使用）
1. 在PgAdmin中新建一个空数据库，改名为Odoo10，使用从运行系统最新备份中拷贝出的odoo10.custom恢复回去，

> 如果使用原数据库名字odoo10，则不会出现丢失图标的问题，如果换一个新的数据库名字如forchart则图标不能正确显示，重新安装一遍应用，应用涉及的图标会变正常，但是系统图标“设置”，“应用”还是不行。 解决方法：还是使用数据库名odoo10，其他数据库先改一个临时的名字

2. 想不安装产品和库存的示例数据，但是使用了各种without_demo=都没有效果，只好到product和stock模块里面把demo和里面的行都注释掉，这下清净了。但是在安装后面的应用时问题又来了，后面应用的demo数据要用到product和stock里demo的ID，所以干脆都装上吧。整个demo织了一张网，不大好删除。

3. 依次安装，“库存”，“采购”，“销售”，“会计”，“质量”，“联系人”，
都装完以后开始删除，顺便把表之间的关系也理顺一下。应该先删除销售单，采购单，及库房的单据，然后删除产品，供应商和客户。

4. 安装“会计”应用。应用会自动找到“中国会计科目表”（技术名称l10n_cn）并安装。细节参照对会计科目的分析。因为目前运行系统为中文环境，所以自动安装的是“中国会计科目表”

> 但是在“会计仪表板”中，提示“这是会计仪表板，如果你还没有安装科目表，请先安装一个 浏览可用的国家”，而安装了应用“中国小企业会计科目表”（l10n_cn_small_business）的系统，在仪表板内有内容。
> 于是复制一份“中国小企业会计科目表”进行改写，具体改写步骤参照OdooTable.md

### 先安装框架，然后各个模块，最后自有模块（ultra+invku）
1. 创建一个空白的数据库invbase，不要选中“装入演示数据”检查框（Load Demonstration data）,因为我们要创建的是一个空白数据库，而不是演示数据库，这一步非常重要，很多人都在此出了问题。
2. 首先安装“会计与财务”模块，并按照手册1.5.3一节的介绍安装会计科目表（依公司情况使用）。
3. 然后再安装库存，销售，采购，质量模块。安装上述模块后，部分菜单可能为英文，你应该点击菜单“设置→翻译→加载翻译”，然后重启Odoo服务。 这样，你就得到了一个能实现本章需求的最小系统
备份这个最小系统到文件夹InvbaseMini
试验手册提到的操作

4. 信息设置：设置公司和用户信息，群组和用户使用导入表的形式。手写公司信息，公司图标指定。
备份到文件夹InvbaseMiniInfo

5. 日历是一个模块，可以进入日历视图；联系人也是一个模块；仪表板是一个应用，也需要安装，也可以定制
上面这三个模块如果需要测试，可以从InvbaseMiniInfo出发安装测试

退回到InvbaseMiniInfo
X. 安装ultra，
X. 安装invku，对invku进行开发并测试



X. 导入m.part和m.assemble，运行数据库函数，执行周期任务
在137.1上测试无误后


问题1，如何把数据库里面的几个表迁移到另一个数据库里。如果不用导入和导出恐怕不行。但没试验过密码如何导出？











### 三，标题

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
