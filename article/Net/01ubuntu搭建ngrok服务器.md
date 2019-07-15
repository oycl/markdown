# 一，搭建ngrok服务
1，方案是使用ECS做服务器端
2，具体步骤详见《ubuntu搭建ngrok服务器.html》，网址http://linfuyan.com/ubuntu-ngrok/，关键字：ngrok.uprogrammer.cn ----------> A记录到你的 VPS IP
3，go1.7.4.linux-amd64.tar.gz是go语言环境，已经验证和ubuntu16.04配合没有问题
4，编译完的文件夹里面有服务器端和客户端及证书。注意编译出来的文件是和域名绑定的，和机器硬件及系统无关。也就是说编译时使用ngrok.uprogrammer.cn这个域名，那在部署ngrokd和ngrok.exe以及ngrok-linux时也必须使用ngrok.uprogrammer.cn这个域名。具体更改位置在
 a,ECS的NGROK_DOMAIN(/etc/profile)：export NGROK_DOMAIN=ngrok.uprogrammer.cn
 b,客户端的ngrok.cfg里面：server_addr: "ngrok.uprogrammer.cn:6062"
   Windows客户端的C:\Windows\System32\drivers\etc\hosts里的
   Linux客户端的/etc/hosts里的 ECS的IP对应域名：39.106.99.110   ngrok.uprogrammer.cn
 c,控制端Windows的hosts文件（C:\Windows\System32\drivers\etc\里面）：39.106.99.110 od.ngrok.uprogrammer.cn


#二，具体步骤

##服务器：
运行命令
./ngrokd -domain="$NGROK_DOMAIN" -httpAddr=":6060" -httpsAddr=":6061" -tunnelAddr=":6062"

还有一个网上的例子指定了key和crt，有空研究一下：./ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="imququ.com" -httpAddr=":8081" -httpsAddr=":8080

如果想后台运行使用setsid ./ngrokd XXX

ngrokd只提供了httpAddr，httpsAddr，tunnelAddr三个端口选项分别在ECS服务器上指定http，https和控制端口，第四个端口也就是远程桌面的连接端口无法指定，每次运行ngrokd程序系统都会随机指定一个高数字端口，如39128端口。这样在ECS上就会出现一个问题：随机端口被屏蔽，控制端对其无法连接。于是每次需要使用命令找出这个端口：
netstat -ntlp |grep ngrokd
然后在ECS要开放这个端口，控制端才能去连接

当然前面三个端口也一定要开放的，不同是只需要设置一次就行，不需要总是更改。
测试端口用
nc -v -w 10 -z 127.0.0.1 6060-6062

更进一步发现了一个指定高端口的方法，就是在客户端指定remote_port参数，可以是命令行也可以写在ngrok.cfg文件里面。这样ECS的端口配置规则设置一次就可以了，具体方法见下面。


##客户端（暴露端）
### Windows客户端：
1，把ECS服务器的公网IP加入到C:\Windows\System32\drivers\etc\hosts里面，如果不让编辑，右击hosts文件，属性，安全，把user的权限都加上，内容
39.106.99.110 od.ngrok.uprogrammer.cn

2，在ngrok.exe旁边建立ngrok.cfg文件，内容如下
```sh
server_addr: "ngrok.uprogrammer.cn:6062"
trust_host_root_certs: false

tunnels:
  ssh:
    remote_port: 43530
    proto:
      tcp: 22
  od:
    remote_port: 6060
    proto:
      http: 8069
```
注意这个配置文件里面空白的地方使用空格，不能用Tab按键。同时这个配置文件设置了两个隧道:一个ssh，另一个od，方便控制和调试。

注意这里的6062端口，和在服务器端使用的6062控制端口一致

3，在cmd里面运行
只启动22端口接收控制如下
ngrok.exe -log=ngrok_log.txt -config="ngrok.cfg" -proto=tcp 22
这里我们看到做远程桌面我们指定了tcp协议和端口22

注：远程桌面的端口是可以更改的 PortNumber=22，改成22是为了和Linux的ssh端口一致
更改位置是注册表的：
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd\Tds\tcp

启动两个隧道的命令如下：
ngrok -log=ngrok_log.txt -config="ngrok.cfg" start od ssh

4，显示状态
Status为online代表内网主机和ECS连接成功，这时候注意显示的tcp://ngrok.uprogrammer.cn:43530->127.0.0.1:22
代表本机开放22端口对应服务器的43530端口

5，做一个测试，cmd中ctrl+c关闭客户端
这时服务器端放掉43530端口，重新连接再占用这个端口，只要服务器端的ngrokd不重启，这个端口就不会改变

6，使用nssm来Windows应用程序做成服务并自动运行。
一般把nssm.exe保存在ngrok.exe一个目录中，然后在cmd里面运行：nssm install ngrok命令调出图形界面，填入程序，安装路径和参数
参数例子
-log=ngrok_log.txt -config="ngrok.cfg" start od ssh
然后点击Install service
常用命令
nssm start ngrok
nssm edit ngrok
nssm restart ngrok


### Linux客户端：
1，修改vim /etc/hosts添加
39.106.99.110 ngrok.uprogrammer.cn
马上自动生效

2，把文件ngrok-linux传到对应机器上/usr/local/games
建立ngrok.cfg文件，内容如上
给个运行权限
chmod u+x ngrok-linux

3，运行
 ./ngrok-linux -config=ngrok.cfg start od ssh
显示和Windows一样的信息表示成功

4，后台运行
a，不再使用 ./ngrok-linux -config=ngrok.cfg -proto=tcp 22 & 不能后台运行，需要使用screen
apt-get install screen
screen -S keepngrok(任意名字)
然后运行./ngrok-linux -config=ngrok.cfg -proto=tcp 22
最后按快捷键CTRL+A+D

b，在用，方案是开机自动运行
在/usr/local/games/里面建立start.sh，内容是：
```sh
cd /usr/local/games
nohup ./ngrok-linux -config=ngrok.cfg start od ssh
```

cd /etc/init.d
vim ngrok
内容是：
```sh
#!/bin/sh
#chkconfig:2345 70 30
#description:ngrok

ngrok_path=/usr/local/games #此处更改为你自己的启动脚本的文件路径
case "$1" in
    start)
        echo "start ngrok service.."
        sh ${ngrok_path}/start.sh
        ;;
    *)
    exit 1
    ;;
esac
```
给个权限
chmod 755 ngrok
加入启动
update-rc.d ngrok defaults
update-rc.d start 70 2 3 4 5
启动服务
service ngrok start
观察状态
systemctl status ngrok.service

##控制端
### Windows控制端
####远程桌面
控制端使用远程桌面程序，其中计算机栏添加：39.106.99.110:43530，
注意上面提到的43530端口一定要在ECS中放行，
控制端不需要去修改hosts文件，当然如果修改了hosts文件就可以使用tcp://od.ngrok.uprogrammer.cn:43530连接ECS了。

####访问Web网站
##### a，前面已经配置好一个隧道od并启动，需要注意的一个问题是：
在控制端的电脑中使用IP:端口连接Windows客户端的远程桌面没有问题，但是在web访问的时候使用IP:端口就提示Tunnel 39.106.99.110:6060 not found
如果启动了od隧道，则在Windows客户端的提示信息为
http://od.ngrok.uprogrammer.cn:6060 -> 127.0.0.1:8069
这时控制端的电脑要去修改hosts文件，添加
39.106.99.110 od.ngrok.uprogrammer.cn
然后在浏览器里面填入http://od.ngrok.uprogrammer.cn:6060才能正常访问

##### b，使用Pycharm + Django创建的项目出现一个web访问问题
Invalid HTTP_HOST header: '10.211.55.6:8000'. You may need to add u'10.211.55.6' to ALLOWED_HOSTS.
1，以上时我访问请求的时候出现的，原因在于Django框架中的创建的一个项目的时候，

2，跑下这个命令：python manage.py runserver 10.211.55.5:8000

3，然后在我本机的浏览器中写入上述IP和端口请求过去：http://10.211.55.6:8000

4，于是就出现了最上面的那个问题；

5，于是就去django-admin.py startproject project-name创建的项目中去修改 setting.py 文件：

ALLOWED_HOSTS = ['*']  ＃在这里请求的host添加了＊

6，于是就成功的访问到了Django的项目了；

### Linux控制端：ssh
在shell里面运行ssh -p 43530 root@39.106.99.110 注意这里面的端口43530使用-p指令指定
