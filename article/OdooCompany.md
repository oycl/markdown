# Odoo公司定制

## 一，模块取舍
### 公司内应用
把E版文件拷贝进custom-addons
### 对外应用 
精简社区版/home/odoo/odoo10/addons里面模块，目前使用module_c_bruno20171113.tar.gz
把自己的模块拷贝进custom-addons

## 二，部署要点
### 1，个性配置
   1）公司信息：把YTO图标更换，填写网址，地址等等
   2）更换D:\odoo\odoo10\addons\web\static\src\img\logo_inverse_white_206px.png为自己logo
   同时把D:\odoo\custom-addons\web_enterprise\static\src\less\app_switcher.less第130行 中的宽度改为适应logo大小，上面图标用192没问题
   3）删除无用菜单“Documentation”“Support”“My Odoo.com account”
   D:\odoo\odoo10\addons\web\static\src\xml\base.xml  第1270行，注释掉前面三个菜单
   4）如果是社区版 把主题的apps替换掉。方法： D:\odoo\custom-addons\web_responsive\views\web.xml 查找apps。企业版不需要
   5）删除页面 “表名-Odoo”里面的Odoo，仅限Odoo10
   D:\odoo\odoo10\addons\web\static\src\js\abstract_web_client.js    54行
   this.set('title_part', {"zopenerp": "Odoo"});删除odoo 或者改成想要的文字
   6）加载语言中文：在Translations菜单里点击 LoadTranslation 选简体中文，确定。
### 2，帮助页面设置
   把view里面点击得到帮助页面的menu指向改到5.55
### 3，配置各个工作组，需要详细记录
   工作组及权限对应

### 4，建立用户，设置密码
   语言简体中文，时区shanghai
   清单如下

### 5，导入最新原数据库数据

### 6，配置数据库导入存储过程

首次配置需要注意装配时间和零件时间,取上下级零件时间大者为装配时间,不然所有装配时间变成当前时间
YT01007的名称螺栓后面有个空格

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