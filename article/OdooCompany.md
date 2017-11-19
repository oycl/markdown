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
6. 企业版D:\odoo\custom-addons\web_enterprise\static\src\xml\base.xml   
    第59行改为"欢迎使用查询系统V3.0"
7. 公司信息：把YTO图标更换，填写网址，地址等等。加载语言中文：在Translations菜单里点击 LoadTranslation 选简体中文，确定。


### 2，配置各个工作组，需要详细记录
   工作组通过res.groups.csv文件导入
```text
id,name
base.group_yanfa,YTO研发中心
base.group_jishu,YTO技术部
base.group_caigou,YTO采购
base.group_shengchan,YTO生产
base.group_kufang,YTO库房
base.group_zhiliang,YTO质量
base.group_xiaoshou,YTO销售
base.group_shuju,YTO数据
```
   
   
### 3，建立用户，设置密码

研发为例res.users.csv，清单如下，语言简体中文，时区shanghai

```text
id,name,login,lang,tz
base.user_D001,唐占龙,tangzhanlong,zh_CN,Asia/Shanghai
base.user_D002,段国臣,duanguochen,zh_CN,Asia/Shanghai
base.user_D003,石玉峰,shiyufeng,zh_CN,Asia/Shanghai
base.user_D004,杨波,yangbo,zh_CN,Asia/Shanghai
base.user_D006,韩守振,hanshouzhen,zh_CN,Asia/Shanghai
base.user_D007,付东海,fudonghai,zh_CN,Asia/Shanghai
base.user_D008,袁照华,yuanzhaohua,zh_CN,Asia/Shanghai
base.user_D009,白荣刚,baironggang,zh_CN,Asia/Shanghai
base.user_D010,郑强,zhengqiang,zh_CN,Asia/Shanghai
base.user_D012,葛庆遥,geqingyao,zh_CN,Asia/Shanghai
base.user_D013,李志,lizhi,zh_CN,Asia/Shanghai
base.user_D014,王清泉,wangqingquan,zh_CN,Asia/Shanghai
base.user_D016,王臣,wangchen,zh_CN,Asia/Shanghai
base.user_D017,宋海东,songhaidong,zh_CN,Asia/Shanghai
base.user_D018,卢双,lushuang,zh_CN,Asia/Shanghai
base.user_D019,陈彤,chentong,zh_CN,Asia/Shanghai
base.user_D020,于广会,yuguanghui,zh_CN,Asia/Shanghai
```
### 4，导入最新原数据库数据
1. 原数据库改密码封存，开始导入数据
2. 首次配置需要注意装配时间和零件时间,取上下级零件时间大者为装配时间,不然所有装配时间变成当前时间
3. YT01007的名称螺栓后面有个空格


### 5，导入存储过程




[back](../)