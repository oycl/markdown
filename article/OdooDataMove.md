# Odoo数据迁移

> 利用SQLServer的的导入导出向导，把每张表导出为.CSV格式，先从没有外键的表开始，源为数据库，
目标为“平面文件目标”，代码页为UTF8格式，使用select语句选择一张表中需要的数据，列与列的分隔符为“，”

## 1，手动编辑表格

| 表名                           | 说明              | 
|:-------------------------------|:------------------|
| TypeList.csv                   | 类型              | 
| SheetList.csv                  | 图幅              | 
| importantList.csv              | 重要度            |
| OwnerList(后改为designer.csv)  | 设计者            |


1. 全部列使用select * 处理
1. 这四个表数据量小且基本固定，手动编辑即可，要注意odoo的扩展标识符机制
1. 在csv文件的第一列使用id作为title，具体内容即为每列的扩展标识符
1. 在实际测试时如果把这个扩展标识符写成1，2，3等序数，发现其在数据库中出现的并不稳定。后来改成字母+数字形式S001，发现好用。
1. 这么注意扩展标识符，是想在外键输入数据的时候采用扩展标识符输入，因为对于odoo的数据库表来说其id是真正的主键，但id对用户来说是隐藏的，外部无法得到这个主键就猜想要用扩展标识符。
最后的结果是根本不用考虑这些，直接用值比如A3放到csv中导入就行

```sh
id,name
S001,A3
S002,A4
S003,A2
S004,A1
```

## 2，使用VIM批量处理的表格
### 1），生成 part.csv
零件表和组合表的部分列，两个表合成一个表，全部导入到part.csv中。
目前知道odoo在导入import的时候可以使用name的值，所以用下面这个sql语句

```sql
SELECT AssembleList.AssembleNo AS No,AssembleList.AssembleName AS
Name,SheetName,TypeName,ImportantName,OwnerName,AssembleList.ValidDate,AssembleList.Remark FROM
AssembleList,SheetList,TypeList,ImportantList,OwnerList WHERE AssembleList.SheetNo=SheetList.SheetNo AND AssembleList.TypeNo=TypeList.TypeNo AND
AssembleList.ImportantNo=ImportantList.ImportantNo AND AssembleList.OwnerNo=OwnerList.OwnerNo
UNION ALL
SELECT PartList.PartNo AS No,PartList.PartName AS Name,SheetName,TypeName,ImportantName,OwnerName,PartList.ValidDate,PartList.Remark FROM
PartList,SheetList,TypeList,ImportantList,OwnerList WHERE PartList.SheetNo=SheetList.SheetNo AND PartList.TypeNo=TypeList.TypeNo AND
PartList.ImportantNo=ImportantList.ImportantNo AND PartList.OwnerNo=OwnerList.OwnerNo
```

得到part.csv后使用gvim做一些处理
1. 替换重要度数据，A，B，C为A级，B级，C级
```regexp
%s/,A,/,A级,/
%s/,B,/,B级,/
%s/,C,/,C级,/
```
2. 每行前加,号
```regexp
%s/^/,/
```
3. 每行前加行号，加part（可能不是必须）
```regexp
%s/^/\=line(".")/
%s/^/part/
```
4. 第一行加标题
这里面由于odoo的外键限制，只能把name当成零件编号字段
```text
id,name,part_name,sheet_no,type_no,important_no,designer_no,date_changed,remark
```
5. 把从sqlserver导出的带毫秒的数字去掉，模式匹配/.\d\{9},例如.012345678,
```regexp
%s/.\d\{9},/,/
```
6. 处理完后在odoo的part的listview中使用导入import把m.part.csv载入
Datetime Format填入：
```regexp
YYYY-MM-DD HH:mm:ss
```
7. excel可以很方便的复制第一列,但是要经过反复的ansi和utf8转换,
如果经过excel处理过,格式可能为
```regexp
YYYY/MM/DD HH:mm
```
8. 验证一下validate，没有问题，点击import

### 2），生成assemble.csv
导出装配表的全部

```sql
select * from assemble
```

1. 每行前加assemble，形成扩展标识符assemble000
```regexp
%s/^/assemble/
```
2. 加标题栏
```regexp
id,father_no,child_no,number,group,remark
```
3. 校验：如果在零件表里面没有这个零件，在装配表里面想要导入的话，
在validate的时候会产生错误，验证了外键约束建立的正确。
4. 准备导入



[back](../)