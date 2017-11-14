# Odoo数据库备份恢复

## 一，表备份和恢复



## 一，库备份和恢复
备份一下
pg_dump -Fc -h 120.92.82.112 -p 5432 -U postgres Harvest >back.dump


恢复一下
C:\Users\fudonghai>pg_restore -h 192.168.1.35 -p 5432 -U postgres -C -d Harvest back.dump
Password:
出现问题，但表结构和数据也都导入进来了
pg_restore: [archiver (db)] Error while PROCESSING TOC:
pg_restore: [archiver (db)] Error from TOC entry 2248; 1262 16385 DATABASE Harvest postgres
pg_restore: [archiver (db)] could not execute query: ERROR:  invalid locale name: "en_US.UTF-8"
    Command was: CREATE DATABASE "Harvest" WITH TEMPLATE = template0 ENCODING = 'UTF8' LC_COLLATE = 'en_US.UTF-8' LC_CTYPE = 'en_US.UTF-8';

C:\Users\fudonghai>pg_restore -h 120.92.82.112 -p 5432 -U postgres -C -d harvest1 harvest1.dump
pg_restore: [archiver (db)] Error while PROCESSING TOC:
pg_restore: [archiver (db)] Error from TOC entry 2244; 1262 16732 DATABASE harvest1 postgres
pg_restore: [archiver (db)] could not execute query: ERROR:  database "harvest1" already exists
    Command was: CREATE DATABASE harvest1 WITH TEMPLATE = template0 ENCODING = 'UTF8' LC_COLLATE = 'C' LC_CTYPE = 'C';

WARNING: errors ignored on restore: 1

7，终极解决方法
数据库在建立使选择对位置不敏感的C类型，模板选择template0
参考这篇文章 http://blog.csdn.net/huguangshanse00/article/details/45865453
备份和恢复加入-c清理命令，就再不报错了
备份
```sh
>pg_dump -Fc -h 192.168.1.35 -p 5432 -U postgres -c harvest1 >Harvest1.dump
pg_dump.exe --host localhost --port 5432 --username "postgres" --no-password  --format custom --blobs --verbose --file "D:\odoo\backups\zuomian1.backup" "odoo"
pg_dump -Fc -h 192.168.137.1 -p 5432 -U postgres -c odoo >"D:\odoo\backups\20171103odoo.dump"
恢复
>pg_restore -h 120.92.82.112 -p 5432 -U postgres -c -C -d harvest1 harvest1.dump
pg_restore.exe --host localhost --port 5432 --username "postgres" --dbname "postgres" --no-password  --verbose "D:\odoo\backups\zuomian.backup"
```

做自动备份时把密码输入环境变量中就不会要密码了
 export PGPASSWORD='odoo'

参考
i was using the following syntax for pg_dump and restore

pg_dump eval --inserts -b -c --encoding UTF8 -Fc -f eval.sql.tar.gz -x -U postgres
createdb -T template0 test -U postgres
pg_restore -d test eval.sql.tar.gz -e -U postgres
the dump was successfull with no errors, but restore makes a some errors, i am dumping and restoring in same machine with same user and privilege all...

i have tried out with other formats also, plain, tar, compressed all gets the same error..

my version of pg is 8.4.11 and psql version is 8.4.11

i am not sure what makes these errors.. can anyone help me
报错
pg_restore: [archiver (db)] Error while PROCESSING TOC:
pg_restore: [archiver (db)] Error from TOC entry 4965; 0 138871 TABLE DATA ir_act_report_xml insigni
pg_restore: [archiver (db)] could not execute query: ERROR:  invalid input syntax for integer: "purchase.order"
LINE 1: ...st for Quotation', 'ir.actions.report.xml', NULL, 'purchase....
                                                             ^
    Command was: INSERT INTO ir_act_report_xml VALUES (350, 'Request for Quotation', 'ir.actions.report.xml', NULL, 'purchase.order', 'purcha...


答
this did the trick

pg_dump database_name -c -Ft -f file_name.tar 

pg_restore -d database_name -c file_name.tar
before this i was trying to restore with out including -c(clean)

even though -c is included in pg_dump it is not used in pg_restore unless we say to use...







### 利用脚本定时执行计划




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