# Odoo公司定制

## 一，模块取舍
### 公司内应用
把E版文件拷贝进custom-addons

### 对外应用 
精简社区版/home/odoo/odoo10/addons里面模块，目前使用addons_c_bruno.tar.gz
解压
```sh
tar -zxvf addons_c_bruno.tar.gz
```
压缩
```sh
tar -zcvf addons_c_bruno.tar.gz addons/
```
把自己的模块拷贝进custom-addons

## 二，部署要点
### 1，个性配置
1. 去掉登录页面的管理数据库D:\odoo\odoo10\addons\web\views\webclient_templates.xml 注释掉232-237行
   如果需要访问这个页面，可以访问http://192.168.137.1:8069/web/database/manager
2. 更换D:\odoo\odoo10\addons\web\static\src\img\logo_inverse_white_206px.png为自己logo
   同时把D:\odoo\custom-addons\web_enterprise\static\src\less\app_switcher.less第130行 中的宽度改为适应logo大小，上面图标用192没问题
3. 删除无用菜单“Documentation”“Support”“My Odoo.com account”
   D:\odoo\odoo10\addons\web\static\src\xml\base.xml  第1270行，注释掉前面三个菜单
4. 如果是社区版 把主题的apps替换掉。方法： D:\odoo\custom-addons\web_responsive\views\web.xml 查找apps。企业版不需要
5. 删除页面 “表名-Odoo”里面的Odoo，仅限Odoo10
   D:\odoo\odoo10\addons\web\static\src\js\abstract_web_client.js    54行
   this.set('title_part', {"zopenerp": "Odoo"});删除odoo 或者改成想要的文字
6. 公司信息：把YTO图标更换，填写网址，地址等等。加载语言中文：在Translations菜单里点击 LoadTranslation 选简体中文，确定。

### 2，帮助页面设置
   把view里面点击得到帮助页面的menu指向改到5.55
   
### 3，配置各个工作组，需要详细记录
   工作组及权限对应
   ('001', '研发中心'),
   ('002', '技术部'),
   ('003', '采购'),
   ('004', '生产'),
   ('005', '库房'),
   ('006', '质量'),
   ('007', '销售'),
   ('008', '数据管理');
   
   
### 4，建立用户，设置密码
   语言简体中文，时区shanghai
   清单如下

### 5，导入最新原数据库数据
首次配置需要注意装配时间和零件时间,取上下级零件时间大者为装配时间,不然所有装配时间变成当前时间
YT01007的名称螺栓后面有个空格


### 6，配置数据库导入存储过程




[back](../)