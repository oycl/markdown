# 东风系统配置修改

## 一，数据库
1，PgAdmin登录问题
linux下
```sh
vim /etc/postgresql/9.5/main/pg_hba.conf
```
找到
```sh
# IPv4 local connections:
host all all 127.0.0.1/32 md5
```
在其下加上一行请求连接的机器IP
host all all 192.168.11.1/16 md5
然后重启数据库
```sh
# service postgresql restart
```

8. 公司信息：把YTO图标更换，填写网址，地址等等。加载语言中文：在Translations菜单里点击 LoadTranslation 选简体中文，确定。

## 二，模块安装
1，web_responsive
2，backend_theme_v10
3，ultra 模块的
m_view.xml 里面第六行 改为 四平东风
static\description文件夹里面的 公司图标
help.html 里面修改



### 三，标题

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
