---
layout: default
---

#Building a Module 构建模块


> Warning
> This tutorial requires having installed Odoo
> 警告
> 本教程需要安装Odoo

##Start/Stop the Odoo server 启动/停止Odoo服务

    Odoo uses a client/server architecture in which clients are 
    web browsers accessing the Odoo server via RPC.
    Odoo 使用 C/S架构，客户端即Web浏览器通过RPC协议访问Odoo服务器。

    Business logic and extension is generally performed on the 
    server side, although supporting client features (e.g. new 
    data representation such as interactive maps) can be 
    added to the client.
    业务逻辑和扩展通常在服务端执行，而只有在添加客户端支持的新特征才会在
    客户端添加代码（例如，在交互映射中新数据的表示）

    In order to start the server, simply invoke the command odoo-bin 
    in the shell, adding the full path to the file if necessary:
    为了启动服务器，只需要在shell中调用命令odoo-bin，如果需要则加入完整的路径名。
    `odoo-bin`

    The server is stopped by hitting Ctrl-C twice from the terminal, 
    or by killing the corresponding OS process.
    通过连发两次 Ctrl-C 命令或杀死相应的系统进程来停止Odoo服务





Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](another-page).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# [](#header-1)Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## [](#header-2)Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### [](#header-3)Header 3

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

#### [](#header-4)Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### [](#header-5)Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### [](#header-6)Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
