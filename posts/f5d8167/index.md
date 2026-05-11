# 芯片设计文件格式：LEF 与 DEF


## LEF文件
LEF是库交换格式 （Library Exchange Format）的缩写，该文件由**foundry**提供，是`P&amp;R`所必须的库文件。

目前，LEF文件主要分为两种:

- **工艺LEF**: 描述“制造工艺规则”，便于`EDA`工具做`GR/DR/DRC`检查等。
- **单元LEF**: 描述“cell 的几何形状和引脚”

LEF的具体信息可以查看[此链接](https://ieda.oscc.cc/train/eda/chip-circuit/Part_2-chip_files/2_3_LEF.html#_1-%E5%B7%A5%E8%89%BAlef).

## DEF文件
DEF最早是布图规划是生成的，作为输入文件相继输入布局布线后完善，最终将转为GDS版图进行物理验证，通过后将进行流片。

其用来描述一个芯片设计在某个**物理实现阶段**中的版图信息，简单来说：
&gt; `.def` 文件描述的是：这个 design 里面有哪些 instance、pin、net，以及它们在版图中的位置、方向、布线、floorplan 等物理信息。

标准`.def`文件由以下部分组成。

### 标题声明

```bash
VERSION 5.8 ;
DIVIDERCHAR &#34;/&#34; ;
BUSBITCHARS &#34;[]&#34; ;
DESIGN BM64 ;
UNITS DISTANCE MICRONS 1000 ;
DIEAREA ( 0 0 ) ( 346182 346182 ) ;
```

该部分需要重点关注的:

- **VERSION**: 表示语法版本, EDA工具根据这个版本去理解语法规则
- **UNITS DISTANCE MICRONS**: 表示 DEF 中的距离单位采用 database unit，简称 DBU, 在该示例中 `1 micron = 1000 DBU`
- **DIEAREA**: 表示芯片边界, 该实例中左下角为`(0, 0)`, 右上角为`(346182 DBU, 346182 DBU)`; 换算成micron为`(346.182 um, 346.182 um)`

### ROW声明
`ROW`服务于 placement，决定 `standard cell` 能放在哪里（`standard cell`的左下角必须跟site的左下角对齐, 简称为**行对其**）。一个具体示例为：

```
ROW ROW_171 core7 2000 240800 N DO 1710 BY 1 STEP 200 0
;
......
```

- **ROW**：固定关键字
- **ROW_171**：该行的行名
- **core7**：该行使用的site名称（site由`工艺LEF`定义，会定义每个site的宽，高，允许的对称性）
- **2000 240800**：该row起点坐标，换算为`um`需要除以`UNITS DISTANCE MICRONS`说明的值
- **N**：row的朝向
- **DO 171 BY 1**：x方向有171个site，y方向有1个site
- **STEP 2000 0**：x 方向相邻 site 之间的起始点距离为 2000 DBU（当site紧挨着时，该值就等于site的宽度），y 方向不重复

&gt;`ROW`的本质就是一串紧挨着排列的`site`。

通过ROW的方向都是**交替出现**，让相邻 row 的电源轨能够共享或对齐，例如 `VDD/VSS rail` 在上下相邻行之间匹配。具体哪种朝向合法，仍然取决于 `LEF` 中 `site` 和 `cell macro` 的 `symmetry` 定义。

![图1](/PostsImgs/IC-Design-Concepts/LefAndDef/std_file_1.png)

### TRACKS声明
`TRACKS` 服务于 detailed routing，决定 metal wire 通常沿哪些轨道布线。具体示例为：

```bash
TRACKS X 0 DO 1730 STEP 200 LAYER MET1 ;
......
```

- **TRACKS**: 固定关键字
- **X**: 表明这一组TRACK的坐标沿X方向变化(即是一组竖直track)
- **0**: 第一条track的坐标
- **DO 1730**: 表明该组tracks共有1730条track
- **STEP 200**: 表明每条track间隔200 DBU
- **LAYER MET1**: 表明该组tracks属于MET1层(一个芯片有多层物理金属层)

下面是上面这个声明的可视化图:

![图2](/PostsImgs/IC-Design-Concepts/LefAndDef/std_file_2.png)

### GCELLGRID声明
`GCELLGRID` 服务于 global routing / congestion analysis，其`STEP`比`TRACKS STEP`大得多. 主要作用就是用于拥塞估计, 具体示例为:

```bash
GCELLGRID X 104100 DO 2 STEP 345
```

- **GCELLGRID**: 固定关键字
- **X**: 定义是在X轴上的切割(即线条是垂直X轴的)
- **104100**: 第一条线的起点坐标(X轴)
- **DO 2**: 重复次数为2
- **STEP 345**: 线条之间的物理距离为 345

### VIAS声明
在芯片中，布线不是在一层完成的，而是分布在多层金属层上, 如果一条 net 想从一层“跳”到另一层，就必须通过VIA来实现, 下面是一个具体示例:

```bash
VIAS 1 ;
- VIA4_1000x1000
 &#43; VIARULE MET5_MET4
 &#43; CUTSIZE 90 90
 &#43; LAYERS MET4 VIA4 MET5
 &#43; CUTSPACING 110 110
 &#43; ENCLOSURE 5 55 5 55
 &#43; ROWCOL 5 5
 ;
END VIAS
```

- **VIAS 1**: 表示本DEF文件中定义了**1**个via类型, 后面这个via可以在NETS里被引用
- **VIA4_1000x1000**: 该via的名称
- **VIARULE MET5_MET4**: 表明这个via是基于`MET5_MET4`规则(该规则由工艺LEF定义)来实例化的
- **CUTSIZE 90 90**: 表明每一个via cut的宽高分别为 90, 90
- **LAYERS MET4 VIA4 MET5**: 表明该via连接的是MET4和MET5层, 物理通孔层位VIA4
- **CUTSPACING 110 110**: 表明via cut在x和y上的间距分别为 110, 110
- **ENCLOSURE 5 55 5 55**: 表明上下金属层对 cut 的“包围距离”, 从左到右值对应的分别是MET4(下层)距离最外围的via cut在x和y方向上的距离分别为5, 55; MET5(上层)距离最外围的via cut在x和y方向上的距离分别为5, 55
- **ROWCOL 5 5**: 表明这个 via 不是单个孔，而是一个 **5 × 5 的 via 阵列**(即5行5列)

![图3](/PostsImgs/IC-Design-Concepts/LefAndDef/std_file_3.png)

### COMPONENTS声明
该部件用于描述本芯片使用的实例以及它们的位置信息(非 FIXED 单元, 在global placement后才有具体的位置信息). 具体示例:

```bash
COMPONENTS 26568 ;
- Valid_reg_p DFFQX1H7L &#43; PLACED ( 269600 340200 ) S
 ;
- Prod_9__reg_p DFFQX1H7L &#43; PLACED ( 156400 249200 ) N
 ;
- Prod_99__reg_p DFFQX1H7L &#43; PLACED ( 159600 336000 ) FN
 ;
 ...
 ...
```

- **COMPONENTS 26568**: 表明当前设计一共有 26568 个instance
- **Valid_reg_p**: 实例名(该cell在该设计中的**唯一名字**)
- **DFFQX1H7L**: 使用的单元类型(由**单元LEF**提供)
- **PLACED ( 269600 340200 )**: 表示该cell的左下角坐标, PLACED是放置状态, 常见的状态有:
    - `UNPLACED` → 还没放
    - `PLACED` → 已放置（但可能还会动）
    - `FIXED` → 固定（不能再移动）
    - `COVER` → 覆盖型（少见）
- **S**: 表示cell朝向(非常重要), 因为cell是有方向的, 需要跟电源轨对齐

place过后, 在该部分你可能会发现插入了大量的TAP CELL, 该单元的作用就是维持芯片的物理完整性和电源完整性， 防止 latch-up 效应

### PINS声明
用来描述**芯片对外的 IO 引脚**（不是内部 standard cell 的 pin）。这些信息会直接影响：

- IO 布局（pad/端口位置）
- 布线起点/终点
- 时序边界（input/output timing）

一个具体示例:

```bash
PINS 1032 ;
- Valid &#43; NET Valid &#43; DIRECTION OUTPUT &#43; USE SIGNAL
  &#43; LAYER MET2 ( -50 0 ) ( 50 520 )
  &#43; PLACED ( 269800 346182 ) S ;
- Rst &#43; NET Rst &#43; DIRECTION INPUT &#43; USE SIGNAL
  &#43; LAYER MET2 ( -50 0 ) ( 50 520 )
  &#43; PLACED ( 172800 346182 ) S ;
- R[255] &#43; NET R[255] &#43; DIRECTION INPUT &#43; USE SIGNAL
  &#43; LAYER MET3 ( -50 0 ) ( 50 520 )
  &#43; PLACED ( 346182 259200 ) W ;
...
...
```

- **PINS 1032**: 一共有1032个IO引脚
- **Valid**: 引脚名
- **NET Valid**: 该IO引脚连接到名称为Valid的net
- **DIRECTION OUTPUT**: 表明这是一个输出端口, 常见的值有:
    - INPUT: 输入
    - OUTPUT: 输出
    - INOUT: 双向
    - FEEDTHRU: 穿越型 (少见)
- **USE SIGNAL**: 表明这是普通信号引脚, 常见的值有:
    - SIGNAL: 普通信号
    - POWER: 电源
    - GROUND: 地
    - CLOCK: 时钟
    - ANALOG: 模拟信号
-  **LAYER MET2 ( -50 0 ) ( 50 520 )**: 表明该IO pin在MET2层, 且形状为矩形(坐标分别为该矩形左下角和右上角的坐标, 该坐标是相对于PLACE基准点的坐标)
- **PLACED ( 269800 346182 ) S**: 表明该pin的在芯片上的绝对基准点和朝向, 通过朝向和该值加上面的相对坐标即可计算出该pin的绝对坐标

### SPECIALNETS声明
该部分用于定义**特殊网络**，通常是指电源（VDD）和地线（VSS）网络。与普通信号线（NETS）不同，特殊网络通常以宽大的“金属带（Stripe）”或“金属环（Ring）”形式存在，以承载大电流并降低压降。具体示例:

```bash
SPECIALNETS 4 ;
- VSS  ( * VSS )
  &#43; ROUTED MET4 1000 &#43; SHAPE STRIPE ( 11000 1320 ) ( * 343080 )
    NEW MET4 1000 &#43; SHAPE STRIPE ( 27000 1320 ) ( * 343080 )
    NEW MET4 1000 &#43; SHAPE STRIPE ( 43000 1320 ) ( * 343080 )
    NEW MET4 1000 &#43; SHAPE STRIPE ( 59000 1320 ) ( * 343080 )
    NEW MET4 1000 &#43; SHAPE STRIPE ( 75000 1320 ) ( * 343080 )
    NEW MET4 1000 &#43; SHAPE STRIPE ( 91000 1320 ) ( * 343080 )
    NEW MET4 1000 &#43; SHAPE STRIPE ( 107000 1320 ) ( * 343080 )
    NEW MET4 1000 &#43; SHAPE STRIPE ( 123000 1320 ) ( * 343080 )
    ...
    ...
    NEW MET5 0 &#43; SHAPE STRIPE ( 107000 313900 ) VIA4_1000x1000
    NEW MET5 0 &#43; SHAPE STRIPE ( 123000 313900 ) VIA4_1000x1000
...
...
```

- **SPECIALNETS 4**：表明有4个特殊网络
- **VSS  ( * VSS )**: VSS表示网络名称; ( * VSS ) 是连接声明, * 为通配符, 表示这个网络要连接到该设计中所有名为VSS的Pin上
- **ROUTED**：表示该网络的线已经固定好
- **MET4**：表示这一层Stripe在MET4上走线
- **1000**：表示该Stripe线宽为 1000 DBU
- **SHAPE STRIPE**：声明该线条为“金属带”。它们通常是一组平行的宽线，横跨整个 Core，像排栅一样为下方的标准单元供电。
- **( 11000 1320 ) ( * 343080 )**：表明该Stripe的起始和终止中心点，`*`表示延续上一个点的坐标(这里表示11000)
- **NEW**: 表示新的Stripe
- **NEW MET5 0 &#43; SHAPE STRIPE ( 107000 313900 ) VIA4_1000x1000**: 这里宽度为0, 是因为这句话的含义是在$(11000, 9900)$ 这个位置，放置一个名为 `VIA4_1000x1000` 的通孔阵列，用来连接 MET5 和下方的 MET4

### NETS声明
该部分定义了电路的**逻辑连接关系**。

```bash
NETS 24042 ;
- Valid
  ( PIN Valid ) ( Valid_reg_p Q )
 ;
- Rst
  ( PIN Rst ) ( _22244_ A ) ( _22253_ B ) ( _22396_ A ) ( _41856_ A )
  ( _42020_ A ) ( _42475_ A ) ( _43067_ A )
 ;
- R[255]
  ( PIN R[255] ) ( _44475_ B )
 ;
- R[254]
  ( PIN R[254] ) ( _41858_ B )
 ;
- R[253]
  ( PIN R[253] ) ( _41874_ A1 )
 ;
...
...
```

- **NETS 24042**：表明接下来定义了24042个NET
- **Valid**: 该NET名称
- **( PIN Valid ) ( Valid_reg_p Q )**: Valid NET包含的pin信息, 即该NET由`IO PIN Valid`和`Valid_reg_p`的`Q pin`组成

以上就是一个`post-place`的def重要部分讲解, 通常来说def还会包含`BLOCKAGE, NDR`声明, 这里就不细说了.


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/f5d8167/  

