# Linux系统环境配置


一，开启root用户
----
有了root用户比较方便，可以少输入好多'sudo'，避免普通用户不在sudor名单的问题。
但是在做删除的时候头脑一定要清醒

```Shell
$ sudo passwd -u root
$ sudo passwd root
```
然后输入密码
测试`su root`

二，安装vim
---
```Shell
# apt-get install vim
```

三，安装openssh server
---
1，管理服务器需要远程连接，有的服务器已经装好就不需要这一步
```Shell
# apt-get install openssh-server
```
2，允许root用户登录，可以对 openssh server进行配置
```Shell
# vi /etc/ssh/sshd_config
```
找到`PermitRootLogin no`一行，改为`PermitRootLogin yes`
然后重启 openssh server
```Shell
# service ssh restart
```

四，给出固定IP地址
---
```Shell
# vim /etc/network/interfaces
```
加入
```Shell
iface ens33 inet static  
address 192.168.5.55
gateway 192.168.4.1
netmask 255.255.254.0
dns-nameservers 202.97.224.68
dns-nameservers 202.97.224.69
```

五，update
--- 
开始前先update ，是同步 /etc/apt/sources.list 和 /etc/apt/sources.list.d 中列出的源的索引，这样才能获取到最新的软件包
```yml
# apt-get update
```
更新一下系统源。upgrade 是升级已安装的所有软件包，升级之后的版本就是本地索引里的，因此，在执行 upgrade 之前一定要执行 update
```yml
# apt-get upgrade 
```


六，设置时区，时间
---
```yml
# vim .profile
```
最后一行加入
```yml
TZ='Asia/Shanghai';export TZ
```
安装ntpdate
```yml
# apt-get install ntpdate
```
同步时间
```yml
# ntpdate time.windows.com
```
系统时间同步硬件时间
```yml
# hwclock --systohc
```
[关于时区参考](http://os.51cto.com/art/201205/336643.htm)



[back](../)
