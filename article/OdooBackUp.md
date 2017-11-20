# Odoo数据库备份恢复及计划任务

## 一，库备份和恢复
### 备份和恢复命令

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

### Linux的crontab执行计划任务

psql需要密码时有以下两种解决方法
Linux下
/var/lib/postgresql/.pgpass
```sh
127.0.0.1:5432:*:postgres:pgdbyto1
```
将数据库密码写到名为PGPASSWORD的环境变量中，然后使用psql等工具就不会提示输入密码了。

```sh
 export PGPASSWORD='pgdbyto1'
```
windows 环境下
C:\Users\fudonghai\AppData\Roaming\postgresql\pgpass.conf
内容
```text
localhost:5432:*:postgres:pgdbyto1
47.94.242.254:5432:*:postgres:pgdbyto1
192.168.5.55:5432:*:postgres:pgdbyto1
```



## 数据库定时执行计划
### 安装，并在`数据库名postgres`内创建相关表等
```sh
# apt-get install pgagent
# su postgres
postgres$ psql
postgres=# CREATE EXTENSION pgagent;
```
> 运行了`CREATE EXTENSION pgagent;`后，pgAdmin里面就会多出一个作业的选项

### 在有数据库的机器上运行pgagent守护进程（windows就是服务）
```sh
# pgagent host=127.0.0.1 dbname=postgres user=postgres password=pgdbyto1
```

### pgAdmin 上运行相应任务

参考文章
[PostgreSQL中定时job执行](http://blog.csdn.net/sunbocong/article/details/77870205)

### 使用pgAgent备份整个数据库
整个数据库备份脚本
1. vim pg_backup.config
```sh
##############################
## POSTGRESQL BACKUP CONFIG ##
##############################
 
# Optional system user to run backups as.  If the user the script is running as doesn't match this
# the script terminates.  Leave blank to skip check.
BACKUP_USER=
 
# Optional hostname to adhere to pg_hba policies.  Will default to "localhost" if none specified.
HOSTNAME=
 
# Optional username to connect to database as.  Will default to "postgres" if none specified.
USERNAME=
 
# This dir will be created if it doesn't exist.  This must be writable by the user the script is
# running as.
BACKUP_DIR=/var/lib/postgresql/
 
# List of strings to match against in database name, separated by space or comma, for which we only
# wish to keep a backup of the schema, not the data. Any database names which contain any of these
# values will be considered candidates. (e.g. "system_log" will match "dev_system_log_2010-01")
SCHEMA_ONLY_LIST=""
 
# Will produce a custom-format backup if set to "yes"
ENABLE_CUSTOM_BACKUPS=yes
 
# Will produce a gzipped plain-format backup if set to "yes"
ENABLE_PLAIN_BACKUPS=yes
 
# Will produce gzipped sql file containing the cluster globals, like users and passwords, if set to "yes"
ENABLE_GLOBALS_BACKUPS=yes
 
 
#### SETTINGS FOR ROTATED BACKUPS ####
 
# Which day to take the weekly backup from (1-7 = Monday-Sunday)
DAY_OF_WEEK_TO_KEEP=5
 
# Number of days to keep daily backups
DAYS_TO_KEEP=7
 
# How many weeks to keep weekly backups
WEEKS_TO_KEEP=5
 
######################################

```
2. vim pg_backup.sh
```sh
#!/bin/bash
 
###########################
####### LOAD CONFIG #######
###########################
 
while [ $# -gt 0 ]; do
        case $1 in
                -c)
                        if [ -r "$2" ]; then
                                source "$2"
                                shift 2
                        else
                                ${ECHO} "Unreadable config file \"$2\"" 1>&2
                                exit 1
                        fi
                        ;;
                *)
                        ${ECHO} "Unknown Option \"$1\"" 1>&2
                        exit 2
                        ;;
        esac
done
 
if [ $# = 0 ]; then
        SCRIPTPATH=$(cd ${0%/*} && pwd -P)
        source $SCRIPTPATH/pg_backup.config
fi;
 
###########################
#### PRE-BACKUP CHECKS ####
###########################
 
# Make sure we're running as the required backup user
if [ "$BACKUP_USER" != "" -a "$(id -un)" != "$BACKUP_USER" ]; then
	echo "This script must be run as $BACKUP_USER. Exiting." 1>&2
	exit 1;
fi;
 
 
###########################
### INITIALISE DEFAULTS ###
###########################
 
if [ ! $HOSTNAME ]; then
	HOSTNAME="localhost"
fi;
 
if [ ! $USERNAME ]; then
	USERNAME="postgres"
fi;
 
 
###########################
#### START THE BACKUPS ####
###########################
 
 
FINAL_BACKUP_DIR=$BACKUP_DIR"`date +\%Y-\%m-\%d`/"
 
echo "Making backup directory in $FINAL_BACKUP_DIR"
 
if ! mkdir -p $FINAL_BACKUP_DIR; then
	echo "Cannot create backup directory in $FINAL_BACKUP_DIR. Go and fix it!" 1>&2
	exit 1;
fi;
 
 
#######################
### GLOBALS BACKUPS ###
#######################
 
echo -e "\n\nPerforming globals backup"
echo -e "--------------------------------------------\n"
 
if [ $ENABLE_GLOBALS_BACKUPS = "yes" ]
then
        echo "Globals backup"
 
        if ! pg_dumpall -g -h "$HOSTNAME" -U "$USERNAME" | gzip > $FINAL_BACKUP_DIR"globals".sql.gz.in_progress; then
                echo "[!!ERROR!!] Failed to produce globals backup" 1>&2
        else
                mv $FINAL_BACKUP_DIR"globals".sql.gz.in_progress $FINAL_BACKUP_DIR"globals".sql.gz
        fi
else
	echo "None"
fi
 
 
###########################
### SCHEMA-ONLY BACKUPS ###
###########################
 
for SCHEMA_ONLY_DB in ${SCHEMA_ONLY_LIST//,/ }
do
	SCHEMA_ONLY_CLAUSE="$SCHEMA_ONLY_CLAUSE or datname ~ '$SCHEMA_ONLY_DB'"
done
 
SCHEMA_ONLY_QUERY="select datname from pg_database where false $SCHEMA_ONLY_CLAUSE order by datname;"
 
echo -e "\n\nPerforming schema-only backups"
echo -e "--------------------------------------------\n"
 
SCHEMA_ONLY_DB_LIST=`psql -h "$HOSTNAME" -U "$USERNAME" -At -c "$SCHEMA_ONLY_QUERY" postgres`
 
echo -e "The following databases were matched for schema-only backup:\n${SCHEMA_ONLY_DB_LIST}\n"
 
for DATABASE in $SCHEMA_ONLY_DB_LIST
do
	echo "Schema-only backup of $DATABASE"
 
	if ! pg_dump -Fp -s -h "$HOSTNAME" -U "$USERNAME" "$DATABASE" | gzip > $FINAL_BACKUP_DIR"$DATABASE"_SCHEMA.sql.gz.in_progress; then
		echo "[!!ERROR!!] Failed to backup database schema of $DATABASE" 1>&2
	else
		mv $FINAL_BACKUP_DIR"$DATABASE"_SCHEMA.sql.gz.in_progress $FINAL_BACKUP_DIR"$DATABASE"_SCHEMA.sql.gz
	fi
done
 
 
###########################
###### FULL BACKUPS #######
###########################
 
for SCHEMA_ONLY_DB in ${SCHEMA_ONLY_LIST//,/ }
do
	EXCLUDE_SCHEMA_ONLY_CLAUSE="$EXCLUDE_SCHEMA_ONLY_CLAUSE and datname !~ '$SCHEMA_ONLY_DB'"
done
 
FULL_BACKUP_QUERY="select datname from pg_database where not datistemplate and datallowconn $EXCLUDE_SCHEMA_ONLY_CLAUSE order by datname;"
 
echo -e "\n\nPerforming full backups"
echo -e "--------------------------------------------\n"
 
for DATABASE in `psql -h "$HOSTNAME" -U "$USERNAME" -At -c "$FULL_BACKUP_QUERY" postgres`
do
	if [ $ENABLE_PLAIN_BACKUPS = "yes" ]
	then
		echo "Plain backup of $DATABASE"
 
		if ! pg_dump -Fp -h "$HOSTNAME" -U "$USERNAME" "$DATABASE" | gzip > $FINAL_BACKUP_DIR"$DATABASE".sql.gz.in_progress; then
			echo "[!!ERROR!!] Failed to produce plain backup database $DATABASE" 1>&2
		else
			mv $FINAL_BACKUP_DIR"$DATABASE".sql.gz.in_progress $FINAL_BACKUP_DIR"$DATABASE".sql.gz
		fi
	fi
 
	if [ $ENABLE_CUSTOM_BACKUPS = "yes" ]
	then
		echo "Custom backup of $DATABASE"
 
		if ! pg_dump -Fc -h "$HOSTNAME" -U "$USERNAME" "$DATABASE" -f $FINAL_BACKUP_DIR"$DATABASE".custom.in_progress; then
			echo "[!!ERROR!!] Failed to produce custom backup database $DATABASE" 1>&2
		else
			mv $FINAL_BACKUP_DIR"$DATABASE".custom.in_progress $FINAL_BACKUP_DIR"$DATABASE".custom
		fi
	fi
 
done
 
echo -e "\nAll database backups complete!"

```

3. 赋予执行权限
```sh
chmod +x pg_backup.sh
```
4. pgAdmin中添加执行，类型是批处理，内容指向脚本
```sh
/var/lib/postgresql/pg_backup.sh
```

###  pgAgent开机自启动
1. 建立脚本/etc/init.d/pgagent
内容
```shell
#!/bin/bash
#
# /etc/init.d/pgagent
#
 
test -f /lib/lsb/init-functions || exit 1
. /lib/lsb/init-functions
 
PIDFILE=/var/run/pgagent.pid
prog=PGAgent
PGAGENTDIR=/usr/bin
PGAGENTOPTIONS="hostaddr=127.0.0.1 dbname=postgres user=postgres password=pgdbyto1"
 
start() {
 log_begin_msg "Starting PGAgent"
 start-stop-daemon -b --start --quiet --exec "$PGAGENTDIR/pgagent" --name pgagent --startas "$PGAGENTDIR/pgagent" -- $PGAGENTOPTIONS || log_end_msg 1
 log_end_msg 0
}
 
stop() {
 log_begin_msg "Stopping PGAgent"
 start-stop-daemon --stop --quiet -n pgagent || log_end_msg 1
 log_end_msg 0
}
 
#
# See how we were called.
#
case "$1" in
 start)
 start
 ;;
 stop)
 stop
 ;;
 reload|restart)
 stop
 start
 RETVAL=$?
 ;;
 status)
 status /usr/bin/pgagent
 RETVAL=$?
 ;;
 *)
 log_success_msg "Usage: $0 {start|stop|restart|reload|status}"
 exit 1
esac

```

2. 改变可执行
```sh
sudo chmod +x /etc/init.d/pgagent
```
3. 添加启动
```sh
sudo  update-rc.d pgagent defaults
```

4. 命令使用
```sh
/etc/init.d/pgagent start
/etc/init.d/pgagent stop
```

参考文章
[How to take Automatic SQL Database Backup ](http://technobytz.com/automatic-sql-database-backup-postgres.html)



卸载pgAgent
```sh
sudo apt-get remove pgagent
```



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