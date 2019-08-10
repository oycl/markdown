# 配置nginx反向代理

安装，新建odoo10_nginx.conf
```sh
# apt-get install nginx 
# rm /etc/nginx/sites-enabled/default
# vim /etc/nginx/conf.d/odoo10_nginx.conf
```
添加内容
```sh
upstream odoo {
    server 127.0.0.1:8069;
}
upstream odoochat {
    server 127.0.0.1:8072;
}

server {
    listen 80;

    # proxy
    proxy_buffers 16 64k;
    proxy_buffer_size 128k;

    proxy_connect_timeout 600s;
    proxy_send_timeout 600s;
    proxy_read_timeout 600s;
    proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;

    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    #gzip
    gzip_types text/css text/less text/plain text/xml application/xml application/json application/javascript;
    gzip on;

    location / {
        access_log /var/log/nginx/odoo10_access.log;
        error_log /var/log/nginx/odoo10_error.log;
        proxy_redirect off;
        proxy_pass http://odoo;
        client_max_body_size 500m;
    }

    location /longpolling {
        proxy_redirect off;
        proxy_pass http://odoochat;
    }
}
```
重启nginx服务，生效
```sh
# service nginx restart
```
> 注意如过设置了longpolling单独转发upstream，下文中设置workers不能为0（打印支持必须大于1）

浏览器访问 http://Server_IP

## 使用nginx日志里面会有报错
后来认为于精简模块无关，内网环境下继续观察这个问题

2017-11-14 05:54:36,216 877 WARNING admin odoo.http: No CSRF validation token provided for path '/'

Odoo URLs are CSRF-protected by default (when accessed with unsafe
HTTP methods). See
https://www.odoo.com/documentation/9.0/reference/http.html#csrf for
more details.

* if this endpoint is accessed through Odoo via py-QWeb form, embed a CSRF
  token in the form, Tokens are available via `request.csrf_token()`
  can be provided through a hidden input and must be POST-ed named
  `csrf_token` e.g. in your form add:

      <input type="hidden" name="csrf_token" t-att-value="request.csrf_token()"/>

* if the form is generated or posted in javascript, the token value is
  available as `csrf_token` on `web.core` and as the `csrf_token`
  value in the default js-qweb execution context

* if the form is accessed by an external third party (e.g. REST API
  endpoint, payment gateway callback) you will need to disable CSRF
  protection (and implement your own protection if necessary) by
  passing the `csrf=False` parameter to the `route` decorator.

目前还没有是用nginx很好的解决方法，有时间参考
[维基百科](https://en.wikipedia.org/wiki/Cross-site_request_forgery)




[back](./)
