# 搭建Samba和Ftp文件服务

## Samba

```sh
# sudo apt install samba samba-common-bin
```
如果不能正常安装，就先update一下

the `samba-common-bin` package isneeded for the smbpasswd tool. By default, users allowed to access shared files need to be
registered with it. We need to register our user, `odoo` for example, and set a password for its file share access:
```sh
$ sudo smbpasswd -a odoo
```
输入密码
```sh
$ sudo vim /etc/samba/smb.conf
```
In the configuration file, look for the [homes] section. Edit its configuration lines so that they
match the settings as follows:
```sh
[homes]
comment = Home Directories
browseable = yes
read only = no
create mask = 0640
directory mask = 0750
```
For the configuration changes to take effect, restart the service:
```sh
$ sudo /etc/init.d/smbd restart
```
然后就可以在windows下面使用映射网络驱动器的方法查看文件
```sh
\\192.168.146.128\odoo
```
When trying to log in with the odoo user, you might encounter trouble with Windows adding the computer's
domain to the username (for example, MYPC\odoo ). To avoid this, use an empty domain by prepending a \ character to the login (for example, `\odoo` ):


## Ftp


### Windows
server-U

### Linux


