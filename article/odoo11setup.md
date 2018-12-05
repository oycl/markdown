________________________________________
Step 1: 
Update apt source-lists:

#sudo apt-get update
#apt-get upgrade

Step 2: 
Create the Odoo user that will own and run the application

#sudo adduser --system --home=/opt/odoo --group odoo

/********
首先创建一个odoo用户
# useradd -m -d /opt/odoo -U -s /bin/bash odoo
********/

Step 3: 
Install and configure the database server, PostgreSQL

#sudo apt-get install postgresql

Once the PostgreSQL is installed, next we setup a new PostgreSQL user for our application. This user will be used for making all the database interaction form the Odoo.

#sudo su - postgres

#createuser --createdb --username postgres --no-createrole --no-superuser --pwprompt odoo
#Enter password for new role: *****
#Enter it again:*****
Finally exit from the postgres user account:

#exit


/********
安装postgresql数据库
ubuntu16.04的postgresql是9.5的, 建议开发和部署使用相同版本。
需要安装不同版本的postgresql可以参考https://devecho.com/v/postgresql-basic/。
# apt-get install postgresql-9.5 postgresql-server-dev-9.5
安装完postgresql数据库后，数据库会自动创建postgres系统用户，在数据库内也会有同名的数据库用户，可以更改这个用户的密码

创建用户
# sudo -u postgres psql
postgres=# create user "odoo" with password 'odoo' createdb;
postgres=# \q
********/

Step 4: 
Install the necessary Python libraries & other needed libraries needed for the application:

Odoo 11 will use python 3.5(which is pre-installed on our Ubuntu), previously it uses python 2.7, so in order to install all dependent libraries easily we`ll install pip3 in our server, as:

#sudo apt-get install python3-pip

Once pip3 is installed on your server, we can proceed with installing other dependent libraries using pip3 as:

#pip3 install Babel decorator docutils ebaysdk feedparser gevent greenlet html2text Jinja2 lxml Mako MarkupSafe mock num2words ofxparse passlib Pillow psutil psycogreen psycopg2 pydot pyparsing PyPDF2 pyserial python-dateutil python-openid pytz pyusb PyYAML qrcode reportlab requests six suds-jurko vatnumber vobject Werkzeug XlsxWriter xlwt xlrd 

Next is to install Odoo Web Dependencies:

#sudo apt-get install -y npm
#sudo ln -s /usr/bin/nodejs /usr/bin/node
#sudo npm install -g less less-plugin-clean-css
#sudo apt-get install node-less

Once all the packages are installed we are ready to proceed with installing Odoo server.


/********
之前odoo10使用的是python2.7
# apt-get install python

python和编译依赖库

不推荐使用deb方式安装python依赖包，可能会因为依赖库版本问题导致一些bug。

python编译依赖
# apt-get install build-essential python-dev python-setuptools -y
如果没有pip使用easy_install安装

python-pip版本比较老，所以这里使用easy_install安装。如果是阿里云，系统镜像自动安装了pip，可跳过此步骤，否则可能导致No module named urllib3问题。
# easy_install pip

lxml依赖
# apt-get install zlib1g-dev libxslt1-dev libxml2-dev -y
ldap依赖
# apt-get install libsasl2-dev libldap2-dev libssl-dev -y
pillow 依赖库
# apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev libfreetype6-dev liblcms2-dev libwebp-dev -y
postgresql 依赖库
apt-get install libpq-dev -y
virtualenv
# pip install virtualenv
********/

Step 5:
Installing ODOO version 11 Community Edition hosted on GITHUB.
Make sure you have GIT installed on your system and if not then install with the simple command:

#sudo apt-get install git
Switch to the Odoo user:

#sudo su - odoo -s /bin/bash
Clone the latest branch of Odoo, in our case it is 11.0 from Github:

如果不建一个子目录。git不下去
#mkdir odoo11
#cd odoo11

#git clone https://www.github.com/odoo/odoo --depth 1 --branch 11.0 --single-branch .
(This might take a little while depending on the speed of your Internet connection.)

后边有评论说下面这句，但是实际运行时安装不成功。后面再有机会安装就不采用requirements.txt，看看效果
Great steps installing Odoo 11. After odoo installation in Step 5, we will need run:

"pip3 install -r requirements.txt" login as odoo inside /opt/odoo directory.


/********
安装odoo
首先使用su命令切换到刚才创建的odoo用户

root# su odoo
odoo@$ cd /opt/odoo
odoo@$ mkdir odoo10
odoo@$ cd odoo10
使用virtualenv创建虚拟环境

odoo@$ virtualenv venv
source venv/bin/activate


下载odoo nightly包

下面的地址和文件名，请替换为你所用的日期。

(venv) odoo@$ wget https://nightly.odoo.com/10.0/nightly/src/odoo_10.0.20170408.tar.gz
(venv) odoo@$ tar xf odoo_10.0.20170408.tar.gz
(venv) odoo@$ cd odoo-10.0-20170408  
安装odoo python模块

(venv) pip install -r requirements.txt -i https://pypi.douban.com/simple
这里必须先安装requirements.txt，因为odoo的源码中，setup.py依赖没有版本号，导致安装的python依赖和odoo requirements.txt里版本不一致。

安装odoo包

(venv) odoo@$ python setup.py install 
(venv) odoo@$ cd ..
(venv) odoo@$ rm -rf odoo-10.0-20170408  
这里把odoo src包作为python的包安装到venv的lib里去，安装完成后删除整个目录。此时多出一个odoo命令，你可以通过这个命令来启动odoo项目。

nightly的src包和github的目录结构略有不同(根目录无odoo-bin等脚本)，适合部署安装用，开发仍然推荐使用git的。
********/

Step 6:
Next step is to create a configuration file for Odoo. But before doing so w`ll first create the directory for storing logs of Odoo server and assigning proper ownership to the directory:

#sudo mkdir /var/log/odoo

#sudo chown odoo:root /var/log/odoo
Next is to create the configuration file for odoo server. Odoo application will use these configuration to run so fill in the configuration as per your requirements.

#sudo vim /etc/odoo-server.conf
A sample configuration file will look like this:

[options]
; This is the password that allows database operations:
admin_passwd = odooyto
db_host = 127.0.0.1
db_port = 5432
db_user = odoo
db_password = odoo
logfile = /var/log/odoo/odoo-server.log
addons_path = /opt/odoo/addons,/opt/odoo/odoo11/addons
(* You need to use the same password you used back in step 3.)

Once the configuration file is created we will set the ownership rights

#sudo chown odoo: /etc/odoo-server.conf
#sudo chmod 640 /etc/odoo-server.conf


Step 7:
Installing the boot script

For the final step we need to install a script which will be used to start-up & shut-down the Odoo server automatically, with the correct user. Here is a Link, you can use this script for the same purpose, you need to paste the same contents of this script to a file in /etc/init.d and call it odoo-server. Once it is in the right place you will need to make it executable and owned by root:

#sudo chmod 755 /etc/init.d/odoo-server
#sudo chown root: /etc/init.d/odoo-server


Step 8:(Optional)
Your Odoo server may encounter some issue(s), so I`m also mentioning few issues & their way around to fix them:

FIX 1: You need to upgrade ”wkhtmltopdf” to 0.12.1  You can also check this LINK
#sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.1/wkhtmltox-0.12.1_linux-trusty-amd64.deb
#sudo dpkg -i wkhtmltox-0.12.1_linux-trusty-amd64.deb
#sudo cp /usr/local/bin/wkhtmltopdf /usr/bin
#sudo cp /usr/local/bin/wkhtmltoimage /usr/bin

FIX 2: Check if your PostgreSQL have proper encoding(UTF-8) or not, used in Odoo Application, if not you can do like this:
#su postgres
#psql
#update pg_database set encoding = pg_char_to_encoding('UTF8');
#exit


Step 9:
Testing the server
To start the Odoo server type:

#sudo /etc/init.d/odoo-server start

You should now be able to view the log-file as:

#tailf /var/log/odoo/odoo-server.log

If there are any problems in this step you need to go back and check, feel free to comment on this blog i`ll try my best to help you

If the log file looks OK, you can check Odoo server running on your browser with url:

http://localhost:8069

BOOT SCRIPT USAGE:

#/etc/init.d/odoo-server {start|stop|restart/reload|status|force-restart|force-stop}

TIPS:

Using ‘status’, we can find information of all runnning odoo daemon servers.
Using ‘force-stop/force-restart’, we can kill all running odoo daemon forcefully, and can start fresh odoo-server daemon.
start/stop will not allow more than one process per daemon.


Step 10:(Optional)
Automating Odoo startup, you may want to automatically Odoo startup on server boots, if yes you can do it as:

#sudo update-rc.d odoo-server defaults

You can now try rebooting you server if you like. Odoo should be running by the time you log back in.

That`s it…
Thanks for reading this blog !!! I hope it will help someone.
