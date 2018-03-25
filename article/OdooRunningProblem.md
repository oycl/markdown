# Odoo运行问题汇总

## 一，操作问题
1，2018年1月25日，发现零件表中没有Z件型，不能导入表。我的第一反应是误操作删除了，只能用之前的数据库恢复。后来查看数据库，发现admin把名称字段从Z改写为A，时间是1月23日11点多，这样把名字改回来就会完全恢复，非常容易。
这样看Odoo使用改写人和改写日期字段是非常必要的，保留了历史证据。而以ID为数据库主键又可以非常容易的恢复数据。

## 二，备份恢复问题
1，
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
