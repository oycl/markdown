一，开启openssh
0，可以开启openssh server方便远程连接，有的服务器已经装好了
# apt-get install openssh-server
允许root登录，可以对 openssh server进行配置
$ sudo vi /etc/ssh/sshd_config
找到PermitRootLogin no一行，改为PermitRootLogin yes
然后重启 openssh server
$ sudo service ssh restart
1，开启root
sudo passwd -u root
sudo passwd root
然后输入密码
测试su root
2，编辑IP地址上网
vim /etc/network/interfaces
加入
iface ens33 inet static  
address 192.168.5.5
gateway 192.168.4.1
netmask 255.255.254.0
dns-nameservers 202.97.224.68
dns-nameservers 202.97.224.69
3，update 
开始前先update ，是同步 /etc/apt/sources.list 和 /etc/apt/sources.list.d 中列出的源的索引，这样才能获取到最新的软件包
# apt-get update 
更新一下系统源。upgrade 是升级已安装的所有软件包，升级之后的版本就是本地索引里的，因此，在执行 upgrade 之前一定要执行 update
# apt-get upgrade 

4，安装vim
apt-get install vim
5，设置时区，时间
 vim .profile
最后一行加入
TZ='Asia/Shanghai';export TZ
安装ntpdate
apt-get install ntpdate
同步时间
ntpdate time.windows.com
系统时间同步硬件时间
hwclock --systohc
关于时区参考 http://os.51cto.com/art/201205/336643.htm
6，
