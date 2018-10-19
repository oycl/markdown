# 搭建一个Web服务器
参考两篇文章
文章一：[利用阿里云ECS搭建微信公众平台服务器](https://blog.csdn.net/xidian_db/article/details/53332775)
文章二：[Nginx+uwsgi+Django 搭建一个简单的web服务器](https://blog.csdn.net/djskl/article/details/46288759?utm_source=blogxgwz5)

web服务器可以直接向用户提供图片、html等静态文件，但不能直接与Django中的python代码交互，所以在它们之间还需要一个中间人来将浏览器的请求传给python代码，并将python的执行结果返回给服务器去响应浏览器的请求。这个中间人就是WSGI，WSGI规定了一些服务器与python代码交互的接口，更像一种协议。

WSGI协议其实是定义了一种server与application解耦的规范，即可以有多个实现WSGI server的服务器，也可以有多个实现WSGI application的框架，那么就可以选择任意的server和application组合实现自己的web应用。例如uWSGI和Gunicorn都是实现了WSGI server协议的服务器，Django，Flask是实现了WSGI application协议的web框架，可以根据项目实际情况搭配使用。

在文章一中使用的是Gunicorn，文章二使用了uWSGI。

全新的安装升级一下系统
```sh
apt-get update
apt-get upgrade
```

本文要实现的一个请求/响应流程如下图所示：

web client  <---> nginx <---> socket <---> uwsgi <---> django



## 一，安装Virtualenv及必要组件
用来创建不同的Python隔离环境，可以保证一个干净的环境。
```sh
adduser wechatmanager
```
输入密码：wshh**1


安装python各类组件
```sh
apt-get install -y python python-pip python-virtualenv
```

切换用户wechatmanager
```sh
$ sudo virtualenv wechat_connector# 此时在mirror文件夹下创建一个虚拟环境
$ cd /home/wechatmanager/wechat_connector 
$ sudo source ./bin/activate   #此时会提示command not found
$ sudo -s #提升至root权限，使用当前用户本身环境
# source ./bin/activate  #正确执行
(wechat_connector)会有显示，表示是在的隔离环境里
(wechat_connector) root@iZ2zedykqg8tzi2w5byZ:~/wechat_connector#

```

安装uwsgi
```sh
(wechat_connector) wechatmanager@iZ2zedykqg8tzi2w5zvyZ:~/wechat_connector$ sudo pip install uwsgi 
```

安装Django
```sh
(wechat_connector) wechatmanager@iZ2zedykqg8tzi2w5bhzvyZ:~/wechat_connector$ sudo pip install Django==1.11.13

```
虽然在虚拟环境安装的Django，但是在虚拟环境里并不能看到Django，也就是无法import，退出到root倒是可以import django并看版本。

```sh
root@iZ2zedykqg8tzi2w5bhzvyZ:~# python
Python 2.7.12 (default, Dec  4 2017, 14:50:18)
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import django
>>> django.VERSION
(1, 11, 13, u'final', 0)
>>>
```


### 1、web client <---> nginx
这个流程跟django没有关系，主要是保证nginx是正常的。
```sh
apt-get install nginx -y
```
安装成功后，启动nginx（或者自动启动），然后在通过浏览器访问：http:127.0.0.1(或者访问域名)，如果没问题的话，会看到成功的提示页面，如果有不清楚的错误，可以通过tail -f /var/log/nginx/error.log来查看。

常用nginx指令
```sh
rpm -ql nginx：查询nginx的安装路径,如果用rpm安装的话。用apt-get安装就查询不到
systemctl start nginx：启动nginx
systemctl restart nginx：重启nginx
systemctl status nginx：查询nginx运行状态
netstat -apn|grep nginx：查询nginx占用的端口
```
可能会遇到nginx.pid没有权限/不存在的问题，这可能是因为nginx没启动，或没有权限去读nginx.pid，运行ps -ef|grep nginx看一下当前nginx程序是否是以nginx用户启动的，如果是的话，应该就是权限问题了，可以把nginx.conf中的user nginx改为user root，然后重启nginx。


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
