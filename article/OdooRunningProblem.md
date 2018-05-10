# Odoo运行维护问题汇总

## 一，操作问题
1，2018年1月25日，发现零件表中没有Z件型，不能导入表。我的第一反应是误操作删除了，只能用之前的数据库恢复。后来查看数据库，发现admin把名称字段从Z改写为A，时间是1月23日11点多，这样把名字改回来就会完全恢复，非常容易。
这样看Odoo使用改写人和改写日期字段是非常必要的，保留了历史证据。而以ID为数据库主键又可以非常容易的恢复数据。

## 二，备份恢复问题
1，备份完的数据库，恢复以后，在登录页面logo，登录框都显示在左上侧（正常是显示在正中间），登录进去后菜单点击无效（也有说是空白的）。系统不能正常工作。
问题使用英文比较准确的描述
blank menu after login
Odoo was working well for a week already and I am starting to customize the program to fit my needs.  After EACH modification, I will logout and log back in just to make sure that it works.  It was working well last night before I left office, but this morning, the following happens:
1) the weblogin screen is mostly screw up.  It appears to be some kind of CSS corruption.  However, it still display the user/pwd for me to login.  The database selection is working well also.
2) After I login, I ONLY see the logo on the left, BLANK menu on top, and BLANK menu on the left.
I have looked through the online help and it appears that many have run into the problem.  And, the ONLY viable fix is to reinstall!?  I find this somewhat disturbing.

I am currently putting Odoo on a Win10 machine.   The odoo version is 9.0.20160620
补充描述
 have already cleared the cache, the cookies, and went as far as using incognito mode.  I have also tried Chrome, IE, Edge (which I normally don't use).  Nothing works.
解决方法
1，有人建议在ubuntu下面，但由于在windows环境，也没法验证
ok.. for your css problem ..try to run following command::
sudo apt-get update
sudo apt-get install nodejs
sudo apt-get install npm
curl -sL https://deb.nodesource.com/setup_0.10 | sudo bash -
sudo apt-get install -y nodejs
sudo npm install -g npm (to update npm)
sudo npm install -g less less-plugin-clean-css
It might help you to resolve your web screen problem.
2，下面这条语句也不好使
UPDATE ir_attachment 
SET mimetype = 'text/javascript' 
WHERE mimetype ='text/plain' 
and datas_fname like '%.js';
3，在数据库层面的有效的解决方法（没有验证）
One of the following commands seem to be able to get the DB working (sometimes):
DELETE FROM ir_attachment WHERE url LIKE '/web/content/%';
DELETE FROM ir_attachment WHERE datas_fname LIKE '%.js%';
DELETE FROM ir_attachment WHERE datas_fname SIMILAR TO '%.(js|css)';
4，在网页层面的有效解决方法
to state the obvious, it is definitely an asset issue. I think that we should have a simple mechanism to force the server to regenerate them. But in the meantime, I may have a workaround (use at your own risk!!!):
1）open Odoo in debug mode（访问localhost:8069\web?debug）
2）find the 'attachment' action (settings/database structure/attachment)
3）go to list view (easier)
4）filter on all attachment which starts with /web/content (those represent your assets)
5）delete them all
With this, the server should be force to regenerate them.
5，总结一下
在数据库备份之前到settings/database structure/attachment把里面的11条记录都删除再备份。或者虽然备份了一个有问题的数据库，但是恢复后在ir_attachment表里面把这11条记录删除。

web.assets_frontend.js
web.assets_common.js
web.assets_frontend.0.css
web.assets_common.0.css
web_editor.assets_editor.js
web_editor.summernote.js
web.assets_backend.js
web.assets_common.js
web_editor.assets_editor.0.css
web_editor.summernote.0.css
web.assets_backend.1.css
web.assets_backend.0.css
web.assets_common.0.css


windows下可以使用的备份的命令
pg_dump.exe --host localhost --port 5432 --username "postgres" --no-password --format custom --blobs --verbose --file "D:\odoo\custom-addons\invku\basedata\01Accounts\backup\06InvbaseUser.backup" "invbase"

## 三，安装问题
1，在安装应用的过程中出现问题:每页记录数无法正常显示，在页面右侧kanban和listview的小图标也无法正常显示的情况。曾经怀疑是备份方法的问题，后来发现是改动源码惹得祸（列表条目从80到150）。


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
