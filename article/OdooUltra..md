# ultraV3.0的实现

## 一，模型和显示之间的关系
### 一个简单的模型

```python
class Sheet(models.Model):
    _name = 'm.sheet'  # _name被称为标识符，用来和其他模块交互。.被转换_成为数据库表m_sheet,当安装应用时会创建数据库表，卸载应用会删除表。
    _table = 'm_sheet'  # 显式的指定表名，指定后就不用_name的值了，后面的表格把这个属性都省略了
    _description = u'图幅表'#在view中“图幅表”会显示出来
    name = fields.Text(u'图幅名称', required=True) #name是一个特殊的字段，这里把它利用起来
```
在odoo中id列会永远作为表的主键，
在数据库会看到
```sql
  CONSTRAINT m_sheet_pkey PRIMARY KEY (id),
```
最后上面的类定义通过ORM转换成postgresql就是这样的
```sql
-- Table: public.m_sheet

-- DROP TABLE public.m_sheet;

CREATE TABLE public.m_sheet
(
  id integer NOT NULL DEFAULT nextval('m_sheet_id_seq'::regclass),
  create_uid integer, -- Created by
  create_date timestamp without time zone, -- Created on
  name text NOT NULL, -- 图幅名称
  write_uid integer, -- Last Updated by
  write_date timestamp without time zone, -- Last Updated on
  CONSTRAINT m_sheet_pkey PRIMARY KEY (id),
  CONSTRAINT m_sheet_create_uid_fkey FOREIGN KEY (create_uid)
      REFERENCES public.res_users (id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE SET NULL,
  CONSTRAINT m_sheet_write_uid_fkey FOREIGN KEY (write_uid)
      REFERENCES public.res_users (id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE SET NULL
)
WITH (
  OIDS=FALSE
);
ALTER TABLE public.m_sheet
  OWNER TO odoo;
COMMENT ON TABLE public.m_sheet
  IS '图幅表';
COMMENT ON COLUMN public.m_sheet.create_uid IS 'Created by';
COMMENT ON COLUMN public.m_sheet.create_date IS 'Created on';
COMMENT ON COLUMN public.m_sheet.name IS '图幅名称';
COMMENT ON COLUMN public.m_sheet.write_uid IS 'Last Updated by';
COMMENT ON COLUMN public.m_sheet.write_date IS 'Last Updated on';
```
每一列的COMMENT最后将变成view里面显示的列名称

### 一对多关系的实现
在零件表中，sheet_no使用了Many2one关联到上面的m_sheet;
```python
class Part(models.Model):
    _name = 'm.part'
    _description = u'零件表'
    name = fields.Char(u'零件编号', size=50, required=True)
    part_name = fields.Text(u'零件名称', required=True)
    sheet_no = fields.Many2one('m.sheet', string=u'图幅', required=True)
```
翻译成postgresql
```sql
CREATE TABLE public.m_part
(
  id integer NOT NULL DEFAULT nextval('m_part_id_seq'::regclass),
  create_uid integer, -- Created by
  remark text, -- 零件备注
  name character varying(50) NOT NULL, -- 零件编号
  important_no integer NOT NULL, -- 重要度
  sheet_no integer NOT NULL, -- 图幅
  
  CONSTRAINT m_part_sheet_no_fkey FOREIGN KEY (sheet_no)
  REFERENCES public.m_sheet (id) MATCH SIMPLE
  ON UPDATE NO ACTION ON DELETE SET NULL,
)
WITH (
  OIDS=FALSE
);
```
这里我们看到sheet_no是integer类型 用外键关联到m_sheet的主键id上。这样我们就对Many2one这种关系有所了解
这种一对多关系的使用在“一”这一端需要建模型，建list，建form，建search，需要做很多工作，好处是具有扩展性
对比而言，使用select字段则简单的多，但是应该没有扩展性，后续还会进行更深入的研究，比如select值的存储位置

### 一对多关系的显示
part和assemble是一对多的关系
在模型中
```python
   father_no_ids = fields.One2many('m.assemble', 'father_no', string=u"装配表")
```
在part端我们使用了一个One2many，这个关键字在数据库中没什么作用，但是在一对多的显示中非常有用
看一下part模型的view，这里面使用了father_no_ids后就可以使用assemble中的字段了

```xml
                        <page string="装配表">
                            <!--给弹出页面取ID使用-->
                            <field name="father_no_ids" context="{'variable1' : name, 'variable2' : active_id}">
                                <tree string="display child" default_order="group">
                                    <field name="child_no"/>
                                    <field name="part_name"/>
                                    <field name="sheet_no"/>
                                    <field name="number"/>
                                    <field name="type_no"/>
                                    <field name="group"/>
                                    <field name="important_no"/>
                                    <field name="designer_no"/>
                                    <field name="date_changed"/>
                                    <field name="remark"/>
                                </tree>
                            </field>
                        </page>
```


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