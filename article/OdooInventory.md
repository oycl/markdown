作者：luohuayong
链接：https://www.jianshu.com/p/157e48704e30

# Odoo10 仓库管理引导

## 仓库位置
为了更好的管理库存，需要为仓库创建叫做位置的分支（例如：发货区、退货区、货架34等等）。原则是涉及盘点的区域都要创建位置。

## 产品类型
### 库存产品
实物类普通产品，被应用在库存管理的所有方面，例如最小库存规则、自动补货等等。
### 消耗品
被设定为数量总是能够满足需求，也就是对可用数量不做监控。
### 服务类产品
由公司或者个人提供的非实物产品。

## 供应链
### 制造Manufacture
通过内部制造的产品或者通过内部资源实现的服务
### 买Buy
产品通过采购订单从供应商处购买
### MTS
按库存生产（Make to Stock），仓库总是试图存储足够的产品以满足客户。如果仓库中的数量不能满足出货，系统会根据最小库存规则自动生成采购订单。
### MTO（Make to Order）
按订单生产（Make to Order），产品只在销售订单确认后根据需求补货，因为会按下单的准确数量进行补货，所以不会更改中间物料。

## 在手/可用数量
### 在手产品
目前仓库中的数量，包含已经被生产订单和销售订单占用的数量。
### 可用产品
是能够满足新的出货需求的产品，这个数量不包含已经被其他订单占用的数量，也不包含采购途中的数量。

## 计量单位
* 默认情况下，产品的通用计量单位是‘件’，所有的产品都可以用它来表示。
* 用更精确的计量单位，例如英镑和千克，需要在库存->设置中激活“一些可能用于买/卖的不同计量单位（高级）”
* Odoo支持同一种类的不同计量单位进行转换，例如体积中的升和加仑。

## 初始库存
系统建立初期，或者新建一个产品时如果需要建立产品的初始库存可以这样做：

* 创建一个库存调整
* 所有都设置好之后，点击开始盘点
* 给每个产品设置"实时数量"和"确认库存"

> 提示 也可以通过excel或者CSV文件导入期初库存。

## 销售流程
Odoo的库存管理和销售、开票流程是集成在一起的，从报价单到发货和最后的开票，整个流程是一个整体。
经典的销售流程如下：

* 销售应用中创建一个报价单，选择一个客户并添加产品
* 销售应用中进行销售确认，报价单变为销售订单同时生成调拨单
* 销售应用中查看发货情况，在销售订单中点击发货按钮可以查看调拨单
* 库存应用中确认调拨单可用，如果产品的供应链设置正确，调拨单的状态将是可用，如果不是，需要手工检查可用。
* 库存应用中移动产品，在调拨单的产品行上手动更新完成数量，或者验证后让系统自动更新。
* 库存应用中确认发货，在调拨单中点击确认按钮

## 采购流程
同销售流程一样，Odoo的仓库也和采购应用集成在一起，经典的采购流程如下：

* 采购应用中创建询价，选择供应商并添加产品和所需数量
* 采购应用中确认订单，确认订单后询价单变成采购单，同时生成调拨单
* 采购应用中查看发货情况，在采购订单中点击发货按钮可以查看调拨单
* 库存应用中查看入库送货，通过仪表盘可以看到待收货的产品
* 库存应用中移动产品，在调拨单中手动更新完成数量，或者通过扫描枪扫描条码，也可以验证后让系统自动更新。
* 库存应用中确认收货单，在调拨单中确认，标识产品已经调拨到库存中。

## 订货规则
良好的库存管理宗旨在于优化库存：不要太低（也可以人工管理缺货）也不要太高（会占据空间并且有产品滞销和过保质期风险）。在Odoo中，系统可以根据订货规则自动创建补货单。创建的方法是在产品视图（必须是库存类产品）点击订货规则，并且需要填写最小数量和最大数量。

## 补货异常问题
如果配置了自动补货，Odoo可以自动产生补货单，通常不需要人工参与，但是有时会有异常出现，基本上都是因为配置出现了问题.一些常见的问题和解决方法：
### 该生产没有产品物料表
需要创建一个物料表或者指定该产品是采购产品
### 采购信息中缺少供应商信息
可以在产品表单的补货页面中定义供应商
### 供应商没有定义地址信息
必须给默认的供应商定义一个产品相关的地址
### 库存中没有足够的数量
需要通过创建订货规则来自动创建订单或者手工创建订单

## 追溯
用产品批次和序列号可以运行追踪，通常是根据贴在产品上的条码进行。批次和序列号可以根据拣货类型被用在收货、内部调拨和发货上。批次号对一个产品批次进行追踪，而序列号则是对每个商品进行追踪。

如何使用追踪：

* 在库存->设置中激活跟踪批次或序列号
* 在产品表单->库存->追踪 设置追踪方式，可以选择唯一序列号或者批次
* 在操作收货、内部调拨、交货的过程中，通过点击序号图标指派批次号或者序列号。

## 路线管理
Odoo处理高级的推式/拉式路线配置：

* 管理产品的制造链
* 为每个产品定义默认位置
* 在仓库中根据业务需求设定路径，例如数量控制，售后服务或者供应商退货
* 租用管理，自动产生从租借地位置的产品退回移动


# OPENERP仓库各类知识详解

## 一、复式库存（DOUBLE-ENTRY STOCK MANAGEMENT）和库存移动（STOCK MOVE）

OpenERP的库存管理采取了独特的复式库存（Double-Entry Stock Management）。什么是复式库存？就是每一笔库存变动，同时记录库存减少和库存增加。例如卖了10辆自行车给客户，OpenERP会在你的库位（Location，这个概念后面再说）减去10辆自行车，再在客户库位（Customers Location）增加10辆自行车。因此，任何时候，OpenERP的所有库位的库存数量加起来总是为零。

系统中，复式库存是通过记录库存移动（stock move）来实现的。例如，前面的卖10辆自行车给客户，系统记录的是：

```xml
产品   |数量 |源库位 |目标库位
自行车 |10   |stock |Partner Locations > Customers
```

这条记录的意思是，有10辆自行车从自己的库位（stock）转移到了客户的库位（Partner Locations > Customers）。任何一笔库存变动，系统中都记录了一笔如此形式的库存移动。在OE中，“库存变动”的概念非常广泛，如库存盘点发现盘亏了2辆自行车，系统记录的是：

```xml
产品   |数量 |源库位 |目标库位
自行车 |2    |stock |Virtual Locations > Inventory Loss
```

这条记录的意思是，有2辆自行车从自己的库位（stock）转移到了“盘点库位”（Virtual Locations > Inventory Loss）。反之，盘盈的话，就是从盘点库位转移到了stock。又如生产，生产领料系统记录库存移动：从原料库位转移到生产库位（Produce Location）。成品入库系统记录库存移动：从生产库位（Produce Location）转移到成品库位。

## 二、实际库存（REAL STOCK）和虚拟库存（VIRTUAL STOCK OR FUTURE STOCK）

系统中不记录各库位的产品库存数量，只记录库存变动，那么，怎么知道某库位中某产品的数量呢？这个其实很容易，例如自行车在库位A中的数量，移入库位A（库存移动的目标库位是库位A）使得自行车数量增加，反之，移出使得自行车数量减少。因此，只要统计自行车的库存移动记录中，目标库位为A的数量，以及源库位为A的数量，两者相减，即得到库位A中自行车的库存数量。

现实中，库存分实际库存（Real Stock）和虚拟库存（Virtual Stock or Future Stock）。实际库存是库位中实有数量，虚拟库存是库位中的将来数量。例如，采购时候，假设今天下单，采购物资要5天后到达。那么，采购员考虑今天要不要下单，以及采购多少数量时候，他应该参考5天后的库存数量来决定。在未来的5天中，有一部分产品已销售出去，将在5天内出库，还有一部分产品之前已下单（采购单或生产单），将在5天内入库。实际库存，加上未来一段时间将要入库的数量，减去未来一段时间将要出库的数量，就是虚拟库存。注意，虚拟库存的数量和时间相关，例如10天后的虚拟库存显然和5天后的虚拟库存不同。

系统如何计算实际库存和虚拟库存呢？还是基于库存移动记录来计算，不过，要用到库存移动记录中的更多信息。系统中实际记录的库存移动（Stock Move）记录，还包括移动状态和计划移动日期、实际移动日期等字段。统计实际库存时，统计那些已经实际发生（移动状态为Done）的库存移动记录的数量即得到。计算虚拟库存时，统计那些实际已经发生的、以及将要发生（移动状态为Valid，以及计划移动日期在计算时间段内）但尚未发生（没有实际移动日期）的移动记录数量即得到。

## 三、仓库（WAREHOUSE）和库位（LOCATION）

仓库是我们通常说的实际“仓库”。一个实际仓库中，通常分为若干货区，如成品区、原料区、废品区，货区还分货架、货层等，货区、货架等形成树状层次结构，这个层次结构在OE中可以用库位建模。

在OE的仓库（Warehouse）设置中，一个仓库分为三个库位（Location）：存货库位（Location Stock）、进货库位（Location Input）、出货库位（Location Output）。

* 存货库位：仓库中存放货物的货区，下面还可按货架、货层等进一步细分。

* 进货库位：仓库中收货的暂存货区，例如存放已收货但尚未验收的临时存放区。简单情况，不需要临时收货区，进货库位可以和存放库位设置成同一个库位。

* 出货库位：仓库中发货的暂存货区，例如存放已发货等待装运的临时存放区。简单情况，不需要临时发货区，出货库位可以和存放库位设置成同一个库位。

库位类似于仓库里面的货区，用于存放产品（注意，OE的产品包括原料、消耗品、成品等一切物资）。**库存移动中记录的源库位、目标库位都是库位，不可用仓库。**OE的库位概念非常灵活，可以是实际库位（库区），也可以是虚拟库位。虚拟库位是物理上不存在，仅用于复式库存的库存移动记录的需要。例如，盘亏时候的库存移动记录，目标库位是“盘点库位”，该库位就是一种虚拟库位。OE的库存移动概念非常广泛，对应的库位也有多种类型。

* 供应商库位（Supplier Location）: 用于从供应商处收货时的库存移动的源库位，是一种虚拟库位。

* 视图库位（View）: 库位树状结构的中间节点，用于组织库位树状结构。该种视图不可在库存移动中使用，但可用于库存统计，即统计其下面的各个库位的总库存。

* 内部库位（Internal Location）: 仓库中实际存放货物的库位，即库区。

* 客户库位（Customer Location）: 用于供货给客户时的库存移动的目标库位，是一种虚拟库位。

* 盘点库位（Inventory）: 用于盘亏时的目标库位，或者盘盈时的源库位。注意，新上OE系统时候也是从该库位中“移动”产品至各库位，以建立期初库存。

* 补货库位（Procurement）: 产品补货单（Procurement Order，这个概念很重要，后面再讲）引起的库存移动的“临时”源库位或目标库位。当系统初次生成补货单时候，系统不能确定补货的来源（不确定是通过购买还是通过生产补货）。只有当系统运行完补货调度计划（Procurement Scheduler）后，才知道补货来源。默认情况下，系统每天运行一次补货调度计划，当然，你可以在任何时候手动执行补货调度计划。补货库位用于初次生成的补货单中的库存移动。每当运行完补货调度计划后，该库位库存数量应为零。

* 生产库位（Production）: 生产时候，生产领料或成品入库时候的目标库位或源库位，是一种虚拟库位。

* 多公司中间库位（Transit Location for Inter-Companies Transfers）: 多公司时候，用于库存移动时候的中间库位。

## 四、库位链（LOCATION CHAIN）

OE的库位间可以串联起来形成库位链。举个简单例子，你希望收到货物后先进入质检区，经质检人员确认后再入库。要实现这个业务，一个简单办法是利用库位链。设置一个质检库位，该库位链接到你的存货库位，每当货物进入质检库位时候（系统记录到一个目标库位为质检库位的库存移动），**系统自动生成一个从质检库位到存货库位的移动单，要求质检人员确认该单子。**

库位链中，从前一库位到下一库位的移动，有三种移动方式：

* 手动转移（Manual Operation）：系统自动生成一张到下一库位的移动单，要求操作员确认后才实际移动。

* 自动转移（Automatic Move）：系统自动生成一张到下一库位的移动单，不需要操作员确认，直接移动。此时通常还会指定库链提前时间（Chaining Lead Time），该时间表示，货物进入库位后，等待该时间，再进入下一库位。这个概念可以用于建模物流路径结点及其时间。

* 自动跳移（Automatic No Step Added）：系统不生成到下一库位的移动单，而是直接修改原库存移动的目标库位为下一库位。

可以利用OE灵活的库位及库位链的概念建模物流处理过程。如：

* 销售发货的多次确认：例如，系统默认的销售发货过程是两次确认，一次是出库前确认待发货产品合格，其次是客户收到货物后联系客户确认收货无误。第一次确认在销售单确认时系统自动产生的装箱单（Packing List，其内容是存货库位Stock到出货库位Output的库存移动）上确认，第二次在第一次确认后产生的运输单（Delivery Order，其内容是从出货库位Output到客户库位Customer的库存移动）上确认。如果不需要第二次确认，那么，只要将Output -> Customer设置成自动转移库位链，则第二次将自动移动，无需确认。

* 跟踪货物进出口的海运路径：例如，假设某产品从美国公司A进口，A公司发货后2天到达美国西岸港口，1个月后到达上海港口，5天后应到达公司仓库。为了跟踪该物流过程，可以建立库位链：Supplier A -> 西岸港 -> 上海港 -> Input -> Stock，并设置该库位链各环节的库链提前时间，则系统可以自动产生库存移动供用户确认货物是否按时到达各节点。

* 管理单个产品的物流路径：安装Location_Path模块后，OE支持为每个产品设置特定的物流路径。

* 管理出租产品：例如某设备A出租给某客户A 3个月，可以为设备A设置库位链：Stock -> Output -> Customer A -> Input ->Stock，并将Customer A -> Input环节的库链提前时间设置为3个月。这样，系统将于3个月后自动产生库存移动单供用户确认设备A是否按时归还。

* 管理委托产品：委托产品是指，存放于客户或供应商处，但产权属于自己的产品，或者，存放于本公司仓库，但产权不属于本公司的产品。例如，对于产权属于自己，但存放于客户处的产品，可以在自己公司的总存货库位下设置一个“客户库位”，用于记录该类产品库存。

## 五、产品补货（PROCUREMENT）

销售缺货时需要及时“补货”，这个补货过程在OE里叫产品补货（Procurement）。OE的补货，是通过手动或自动生成补货单（Procurement Order）来实现。有多种情况会引发补货：

* 1）仓管员发现某产品库存太低，他可以手动生成一张补货单（Procurement Order）。在产品画面，右边有个“Make Procurement”链接，当查看库存时发现产品库存太低，可以点击该链接手工填写补货单。

* 2）按库存生产（Make to Stock）的产品，当该产品库存低于最低库存（minimum stock rules）时，系统会自动生成补货单。系统有一个批处理Job，该Job会定期执行（默认为一天一次），该Job根据设定的最低库存规则（minimum stock rules），如果库存不够时，自动生成补货单。

* 3）按订单生产（Make to Order）的产品，确认销售订单时候，系统会自动生成补货单。

* 4）按库存生产（Make to Stock）的产品，根据主生产计划（master production schedule）生成补货单。

随着补货单的执行，系统会产生一系列的动作。如自动生成采购单（Purchase Order），自动生成制造单（Manufacturing Order），自动生成任务（Task）。具体采取什么动作，取决于产品（Product）的供应方法（Supply Method）、补货方法（Procurement Method）、产品类型（Product Type）三个属性的设置。

OE支持两种产品补货方法：按库存生产（Make to Stock）、按订单生产（Make to Order）。

* 按库存生产（Make to Stock）: 表示从仓库取货供给客户的产品，如分销商的情况，或者季节性产品。

* 按订单生产（Make to Order）: 表示有销售订单时，根据订单去采购或生产的产品，该种产品不会入库。

OE支持两种产品供应方法：生产（Produce）、购买（Buy）。

* 生产（Produce）: 表示该产品通过自己生产来供应，或者该服务是通过内部资源来提供。

* 购买（Buy）: 表示该产品或服务通过购买来供应。

OE支持三种产品类型：可库存品（Stockable）、消耗品（Cosumable）、服务（Service）

* 可库存品（Stockable）：需要计量其库存数量的物资，通常的有形产品都是该类型。

* 消耗品（Cosumable）：不需要计量其库存数量的物资，通常是低值易耗品，如小螺钉等。

* 服务（Service）：非有形物，OE中将服务也看做产品。如咨询服务、软件开发服务。

对于可库存产品，补货单（Procurement Order）的后续动作：
```xml
补货方法 |供应方法(Produce) |供应方法(Buy)
MTS      |等待              |等待
MTO      |自动生成生产单    |自动生成采购单
```

对于服务产品，补货单（Procurement Order）的后续动作：
```xml
补货方法 |供应方法(Produce) |供应方法(Buy)
MTS      |-                 |-
MTO      |自动生成项目任务  |自动生成服务外包合同
```

下面举个复杂点的例子说明OE的补货（Procurement）过程：

1）销售出某MTO的产品A

2）销售单(SO:Sale Order)确认时候，系统自动生成产品A的补货单(PO:Procurement Order)

3）该PO导致系统生成产品A的制造单(MO:Manufacturing Order)

4）随着该MO的执行，系统会根据BOM计算产品零部件的补货单

5）零部件补货单会导致系统生成库间移动单（INT：Internal Moves，相当于领料单）

6）如果零部件是MTO的产品，该INT又会触发新的PO及MO，如果该零部件是MTS的，且库存不足领料数量，则系统将处于待料状态（Waiting）

7）待料状态下，等待一天后，最小库存规则会自动引发该零部件的补货单，该补货单又会产生采购单（PO：Purchase Order）

8）采购员确认系统生成的采购单，且采购零部件入库后，待料状态结束，生产可以继续进行。

从上述补货过程可以看出，有多种原因会导致补货过程中断（Procurement Exception），补货单会记录导致异常的原因。设置错误是导致自动补货异常的最常见原因。

* 产品未定义BOM(No bill of materials defined for production): 这种情况，需要定义BOM，或者将产品的供应方法改成购买（Buy）。

* 找不到采购单的供应商（No supplier available for a purchase）: 这种情况是因为没有定义产品的供应商，需要在Product界面的Supplier页定义产品默认供应商。

* 找不到供应商的地址（No address defined on the supplier partner）:这种情况，需要设置该供应商的地址，系统自动生成采购单时需要该地址。

* 库存不足（No quantity available in stock）:这种情况需要定义最小库存规则，以使系统能够自动补货，或者手工录入补货单补足库存。

## 六、库存价值（STOCK VALUATION）

库存价值计算比较难，财务经理给出的库存价值往往和库管经理给出的库存价值相差很多，所谓库存“两张皮”现象。在OE中，非常巧妙的将“两张皮”统一成了一张皮。OE中统一“两张皮”的办法是，每一笔库存移动都会自动生成会计上的一笔库存移动分录。因为系统自动生成，避免了人为错误，故而会计分录和库存移动能够保持一致，财务经理从会计分录计算的库存价值和库管经理从库存移动计算出的库存价值总是相等的。

为了统一库存价值的“两张皮”，在OE中需要设置如下一些基础参数：
* 产品（Product）画面的Stock Input Account、Stock Output Account，
* 库位（Location）画面的Stock Input Account、Stock Output Account。

产品的Stock Input Account：该产品入库时，入库分录的贷方Credit。产品入库时候，借方Debit通常固定是“库存商品”。

产品的Stock Output Account：该产品出库时，出库分录的借方。产品出库时候，贷方通常固定是“库存商品”。

库位的Stock Input Account：产品入该库位时，入库分录的借方。该参数通常是“库存商品”下的以库位名命名的二级科目，如“A库”。但如果是“盘点库位”或“生产库位”，不要设置成“库存商品”的二级科目，你可以设置该参数为“盘点库”、“生产库”，或者不设。

库位的Stock Output Account：产品出该库位时，出库分录的贷方。该参数通常也是“库存商品”下的以库位名命名的二级科目，如“A库”。

考虑如下例子，

产品“自行车”的Stock Input Account＝“外库”，Stock Output Account＝“外库”

A库位的Stock Input Account＝“库存商品 － A库”，Stock Output Account＝“库存商品 － A库”

盘点库位的Stock Input Account＝“盘库”，Stock Output Account＝“盘库”

自行车单价200元，期初自行车库存数量为0，依次发生业务：

1. 采购入库自行车10辆，入A库位；
2. 销售出库自行车5辆，从A库出；
3. 月末盘点盘亏1辆自行车。

当确认上述库存移动时候，系统会自动生成如下会计分录：

1）采购入库
```xml
产品   |数量 | 源库位   | 目标库位
自行车 |10   | Supplier | A库位

会计分录
借：库存商品 － A库 2000
贷：外库 2000
```

2）销售出库
```xml
产品 |数量 | 源库位 | 目标库位
自行车 | 5 | A库位 | Customer

会计分录
借：外库 1000
贷：库存商品 － A库 1000
```

3）盘亏
```xml
产品 |数量 | 源库位 | 目标库位
自行车 | 1 | A库位 | 盘点库位

会计分录
借：盘库 200
贷：库存商品 － A库 200
```

从系统的记录中，库管经理很容易得出结论：A库位4辆（10辆－5辆－1辆），盘点库位1辆。

财务经理很容易得出结论：A库位价值800元（2000元－1000元－200元），盘点库位价值200元。

盘点库位的价值表示盘亏了200元。


