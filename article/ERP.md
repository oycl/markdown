# ERP原理

## 一，打通PLM和ERP
好处：让设计人员去看库存，可以有效减少库存。以前的概念是下生产计划看库存，现在继续前移到设计人员处，
对设计人员提出了更高的要求。

关于BOM
一定要有两套BOM，设计BOM和工艺BOM，设计BOM给工艺后，工艺要做出调整，制作自己的BOM，ERP拿工艺BOM计划生产

PDM专攻文件管理，文件应该是有版本号的，可以保存历史版本。这个文件就是图纸和文档


项目管理，把项目细化到子任务，在子任务内规定工期，然后用提交的图纸和文档来完成子任务

基础部分
PDM
项目管理
工艺管理：工艺路线和工艺卡片
ERP
出售同时在线人数，有意思

实施ERP的难点在于如何改变企业原有的习惯
无论是设计，工艺，生产，采购，库房

## MRP和ERP
MRP现在已经很少提了，它早已变成了ERP里面的一个小模块，而且名称也基本不叫MRP。

MRP的原理是根据客户需求（订单），确定产品生产数量，再根据产品的BOM（物料清单），对产品进行逐渐分解，分解到具体物料。然后根据分解结果制定采购计划和生产计划，采购来必要的零件后，进行生产和装配。

ERP本质上来说是把上述的业务过程进行了扩展，根据采购订单生成应付凭证，根据销售订单生成应收凭证，根据生产计划进行成本核算，然后把这些凭证转移到财务模块，由财务模块进行记账、核算。

现代的ERP在此基础上进行了进一步的功能增强，主要是加强了流程的内部控制，避免风险（如发票、订单、出入库单等三单匹配等功能），此外，也增强了对分析决策的支持。

主流的ERP厂商也在向销售与客服（CRM系统）、供应链管理系统（SCM）等领域扩展，力图把企业内部的管理链条与对外的业务协作链条串联起来，形成整个企业生态链，这也是所谓“ERP II”的概念。


### 需要想明白的几个问题
1,企业迫切需要解决的困扰？（管理诊断）
2,描述形成困扰的业务流程
3,分析有问题的流程
4,提出改进方案（流程重组）
5,设定ERP预期目标和指标


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

