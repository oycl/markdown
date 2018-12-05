# Linux系统环境配置

## 1，开启root用户

> 有了root用户比较方便，可以少输入好多'sudo'，避免普通用户不在sudor名单的问题。但是在做删除的时候头脑一定要清醒

```sh
$ sudo passwd -u root
$ sudo passwd root
```

然后输入密码
测试`su root`

## 2，update

开始前先update ，是同步 /etc/apt/sources.list 和 /etc/apt/sources.list.d 中列出的源的索引，这样才能获取到最新的软件包
```sh
# apt-get update
```
更新一下系统源。upgrade 是升级已安装的所有软件包，升级之后的版本就是本地索引里的，因此，在执行 upgrade 之前一定要执行 update
``` sh
# apt-get upgrade
```

## 3，安装vim,unzip,git,npm,sudo

> 已经有了就不用安装了

```sh
# apt-get install vim
# apt-get install unzip
# apt-get install git
# apt-get install sudo
# apt-get install npm
```

> 国内安装NPM(Node.js包管理工具)速度很慢，解决方法是使用淘宝的镜像服务器

```sh
npm config set registry=http://registry.npm.taobao.org
```

> 或者设置代理

```sh
npm config set proxy=http://127.0.0.1:8087
npm config set registry=http://registry.npmjs.org
```


## 4，安装openssh server

1. 管理服务器需要远程连接，有的服务器已经装好就不需要这一步
```sh
# apt-get install openssh-server
```
2. 允许root用户登录，可以对 openssh server进行配置
```sh
# vi /etc/ssh/sshd_config
```
找到`PermitRootLogin no`一行，改为`PermitRootLogin yes`

3. 防止连接中断
	* 在/etc/ssh/sshd_config最后一行增加`ClientAliveInterval 60`, 
ClientAliveInterval指定了服务器端向客户端请求消息的时间间隔, 默认是0, 不发送。
而ClientAliveInterval 60表示每分钟发送一次, 然后客户端响应, 这样就保持长连接了。
	* 这里比较怪的地方是：不是客户端主动发起保持连接的请求(如FTerm, CTerm等),而是需要服务器先主动。
	* 另外,至于ClientAliveCountMax(这个值我没有发现), 使用默认值3即可。ClientAliveCountMax表示服务器发出请求后客户端没有响应的次数达到一定值, 就自动断开。正常情况下, 客户端不会不响应。

4. 重启 openssh server
```sh
# service sshd restart
``` 

## 5，设置固定IP地址

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

## 6，设置时区，时间

```sh
# vim .profile
```
最后一行加入
```sh
TZ='Asia/Shanghai';export TZ
然后退出，运行生效
# source .profile
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
### 关于时区的讨论

1. 导入时间和用户的时区挂钩,会重新计算存储到数据库中
    1. admin用户选择Hongkong,则数据库导入时间-8做存储，网页显示会+8做调整（这个调整不随用户设置时区改变），然后显示正常
    1. admin用户选择utc，则数据库导入时间不变，网页显示+8调整看起来就会错误
以上测试和服务器中数据库的时区设置无关，已经测试。

2. odoo网页显示时间只跟电脑时区挂钩。无论怎么改当前用户的时区，是无效的。

3. 导出时间和存储挂钩,实际字段存储的值就是导出值,这时候又把时区忽略了

我们采用每个用户设置为Asia/Shanghai，页面显示正确，存储差8小时，导出差8小时


[关于时区参考](http://os.51cto.com/art/201205/336643.htm)


[back](../)
