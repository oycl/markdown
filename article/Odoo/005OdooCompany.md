# Odoo公司定制

## 一，主体框架
使用odoo10_c_bruno.tar.gz，整体考入server后解压
解压
```sh
tar -zxvf odoo10_c_bruno.tar.gz
```
压缩
```sh
tar -zcvf odoo10_c_bruno.tar.gz odoo10/
```

## 二，模块取舍
### 公司内应用
把E版文件拷贝进custom-addons

### 对外应用 
精简社区版目前使用addons最小化模块40个含完整库存.rar，因为文件少，可以解压后传入
/home/odoo/odoo10/addons

把自己的模块拷贝进
/home/odoo/odoo10/custom-addons

注意自己的用户名为odoo，如果是root需要给权限

## 二，部署要点
### 1，个性配置
1. 去掉登录页面的管理数据库D:\odoo\odoo10\addons\web\views\webclient_templates.xml 注释掉232-237行
   如果需要访问这个页面，可以访问http://192.168.137.1:8069/web/database/manager
2. 企业版：更换D:\odoo\odoo10\addons\web\static\src\img\logo_inverse_white_206px.png为自己logo
   同时把D:\odoo\custom-addons\web_enterprise\static\src\less\app_switcher.less第130行 中的宽度改为适应logo大小，上面图标用192没问题
3. 删除无用菜单“Documentation”“Support”“My Odoo.com account”
   D:\odoo\odoo10\addons\web\static\src\xml\base.xml  第1270行，注释掉前面三个菜单
4. 如果是社区版（需要先安装web_responsive） 把主题的apps替换掉。方法： D:\odoo\custom-addons\web_responsive\views\web.xml 查找apps注释掉，位置在221行。企业版不需要。
5. 删除页面 “表名-Odoo”里面的Odoo，仅限Odoo10
   D:\odoo\odoo10\addons\web\static\src\js\abstract_web_client.js    54行
   this.set('title_part', {"zopenerp": "Odoo"});删除odoo 或者改成想要的文字
6. 更改ultra应用每页显示记录数量从80到150，包括one2many字段使用的page页面。
找到D:\odoo\odoo10\addons\web\static\src\js\views\list_view.js 
第144行 80改成150，重启服务，升级模块。（这次升级可能导致原有模块的安装错误，需要谨慎。）
改过后发现这不是一个全局设定，在“应用”菜单里面其值是40，在“外部标识”里面，其值为80
这也就是在windows action里面使用limit限制了数目

7. 企业版D:\odoo\custom-addons\web_enterprise\static\src\xml\base.xml
    第59行改为"欢迎使用查询系统V3.0"
8. 公司信息：把YTO图标更换，填写网址，地址等等。加载语言中文：在Translations菜单里点击 LoadTranslation 选简体中文，确定。
9. 使用企业版，有时候安装讨论模块会使数据库到期。就想恢复到社区版。
使用云模拟运行系统做测试，放入所有模块变成企业版，系统会初始化一个数据库，然后导入群组，安装ultra，删除整个数据库，用运行系统备份数据恢复回来，这样云和运行系统就一样了。做一次快照备份防止出现问题。

* 测试：直接安装库房，再安装质量观察效果，结果是装完库房就提示过期。
* 方案：实际上在目前情况下只安装了企业版的web_enterprise，web_gantt，web_mobile，grid，依次在应用中卸载。把企业版所有模块从硬盘移除，只保留质量quality模块。重启系统看看。安装库房和质量模块再做一次快照备份防止出现问题。
* 测试：社区版的backend主题能调出搜索框看到很多不该看到的内容，目前修改还很费事（后来发现web_responsive-10.0.1.2.1这个版本好用，升级web_responsive-10.0.1.2.2后反倒有问题）。

10. 在社区版最小模块状态下，也可以安装库房Inventory和质量Quality模块，然后再装主题。


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
base.group_caiwu,YTO财务
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
D001研发，姓+电话，666
G001工艺，1111
X001销售，1111
Z001质量，zl111
S001生产，sc111
K001库房，ku111
C001采购，cg111




### 4，导入最新原数据库数据
1. 原数据库改密码封存，开始导入数据
2. 首次配置需要注意装配时间和零件时间,取上下级零件时间大者为装配时间,不然所有装配时间变成当前时间
3. YT01007的名称螺栓后面有个空格



[back](./)
