# Linux系统环境配置

## 一，开启root用户
有了root用户比较方便，可以少输入好多'sudo'，避免普通用户不在sudor名单的问题。
但是在做删除的时候头脑一定要清醒
```sh
$ sudo passwd -u root
$ sudo passwd root
```
然后输入密码
测试`su root`

## 二，安装vim
```sh
# apt-get install vim
```

## 三，安装openssh server
1，管理服务器需要远程连接，有的服务器已经装好就不需要这一步
```sh
# apt-get install openssh-server
```
2，允许root用户登录，可以对 openssh server进行配置
```sh
# vi /etc/ssh/sshd_config
```
找到`PermitRootLogin no`一行，改为`PermitRootLogin yes`
然后重启 openssh server
```sh
# service ssh restart
```
?putty inactive

## 四，设置固定IP地址
```sh
# vim /etc/network/interfaces
```

加入
```sh
iface ens33 inet static  
address 192.168.5.55
gateway 192.168.4.1
netmask 255.255.254.0
dns-nameservers 202.97.224.68
dns-nameservers 202.97.224.69
```
然后重启网卡
```sh
# /etc/init.d/networking restart 
```
在虚拟机上的结果是原IP和新IP都好使，重启系统后只有新IP生效

## 五，update
开始前先update ，是同步 /etc/apt/sources.list 和 /etc/apt/sources.list.d 中列出的源的索引，这样才能获取到最新的软件包
```sh
# apt-get update
```
更新一下系统源。upgrade 是升级已安装的所有软件包，升级之后的版本就是本地索引里的，因此，在执行 upgrade 之前一定要执行 update
``` sh
# apt-get upgrade 
```

## 六，设置时区，时间
```sh
# vim .profile
```
最后一行加入
```sh
TZ='Asia/Shanghai';export TZ
```
安装ntpdate
```sh
# apt-get install ntpdate
```
同步时间
```sh
# ntpdate time.windows.com
```
系统时间同步硬件时间
```sh
# hwclock --systohc
```
[关于时区参考](http://os.51cto.com/art/201205/336643.htm)


[back](../)
