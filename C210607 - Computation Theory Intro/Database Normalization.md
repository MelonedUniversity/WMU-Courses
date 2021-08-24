# Database Normalization

数据库范式化

## 概要

**数据库范式化**是指将数据库设计成符合数据库范式的过程。在关系型数据库中，**范式**（Normal Form）是在数据库的库表关系上的一系列规范要求。满足这类规范要求的数据库将具有更少的数据冗余，有时也可能带来潜在的效率提升或拓展性$^{*1}$。常见有六大范式，它们分别是1NF、2NF、3NF、BCNF、4NF和5NF。

本文仅概要性地介绍关系型数据库中各级范式的概念及关联。

## 相关概念

> 以所有字段为全集 $U$。

### 函数依赖

函数依赖（Functional Dependency）：对于 $U$ 上的关系模式 $R(U)$，若 $X,Y⊂U$，且对于 $x∈X,y∈Y,xRy$ 中 $x$ 的每一个取值都有唯一的 $y$ 与之对应，则称 $Y$函数依赖于$X$ 或 $X$函数决定$Y$，记作 $X→Y$。

平凡函数依赖（Trivial Functional Dependency）：在 $X→Y$ 的前提下，$Y⊆X$，则 $X→Y$ 是平凡的函数依赖。  
非平凡函数依赖（Non-Trivial Functional Dependency）：在 $X→Y$ 的前提下，$Y⊊X$，则 $X→Y$ 是非平凡的函数依赖。

部分函数依赖（Partial Functional Dependency）：在 $X→Y$ 的前提下，$∃S⊂X,S→Y$，则称 $Y$部分函数依赖于$X$，记作 $X\stackrel{P}\longrightarrow Y$。  
完全函数依赖（Full Functional Dependency）：在 $X→Y$ 的前提下，$∀S⊂X,S\nrightarrow Y$，则称 $Y$完全函数依赖于$X$，记作 $X\stackrel{F}\longrightarrow Y$。  
传递函数依赖（Transitive Functional Dependency）：在 $X→Y,Y→Z$ 的前提下（$Z⊂U$），若 $Z⊊Y,Y⊊X,Y\nrightarrow X$ $^{*2}$，则称 $Z$传递函数依赖于$X$，记作 $X\stackrel{T}{\longrightarrow}Z$。  

### 字段与键$^{*3}$

字段：表的某一列。  
记录：表的某一行。  
候选键（Candidate Key）：若 $K⊂U,K\stackrel{F}\longrightarrow U$，则 $K$ 为候选键。  
主键（Primary Key）：从多个候选键中任意挑选的一个，将其作为主键。主键是候选键的真子集。  
主字段：所有参与构成候选键的字段。  

## 1NF

字段不可分。

下面这个表不满足1NF，因为Index字段被拆分了。

<table>
<thead><tr>
<th colspan = "2">Index</th><th>Fruit</th><th>Price</th>
</tr></thead>
<tbody>
<tr><td>1</td><td>A</td><td>Apple</td><td>5$</td></tr>
<tr><td>2</td><td>B</td><td>Berry</td><td>6$</td></tr>
<tr><td>3</td><td>C</td><td>Cherry</td><td>7$</td></tr>
</tbody>
</table>

关系型数据库均满足1NF。

## 2NF

满足1NF，且非主字段完全函数依赖于候选键。

在下表中：

|   DP   | Fruit  | Price |
| :----: | :----: | :---: |
| Sep.30 | Apple  |  6$   |
| Oct.6  | Apple  |  6$   |
| Oct.6  | Berry  |  6$   |
| Oct.6  | Cherry |  7$   |
| Oct.7  | Cherry |  7$   |

$\rm\{DP,Fruit\}$ 是候选键：对于DP和Fruit构成的集合 $\rm\{DP,Fruit\}$，$\rm\{DP,Fruit\}$ 的任一取值均有唯一的行与之对应，且其真子集均不能成为成为候选键$^{*4}$，所以 $\rm\{DP,Fruit\}⊂U,\{DP,Fruit\}\stackrel{F}\longrightarrow U$ 成立。  
Price是非主字段：DP与Fruit为主字段是显然的，而 $\rm\{DP,Price\}$ 与 $\rm\{Fruit,Price\}$ 均不能成为候选键$^{*5}$。

Price部分函数依赖于 $\rm\{DP,Fruit\}$：由于 $\rm\{DP,Price\}→Price$，而 $\rm\{Fruit\}⊂\{DP,Fruit\},\rm\{Fruit\}→Price$ 成立，所以 $\rm\{DP,Fruit\}\stackrel{P}\longrightarrow Price$ 。

正因为 $\rm\{DP,Fruit\}\stackrel{P}\longrightarrow Price$，所以上表不满足2NF。

下二表是对上表进行*模式分解*$^{*6}$的结果，均满足2NF。

<div><div style="display: inline-block">

|   DP   | Fruit  |
| :----: | :----: |
| Sep.30 | Apple  |
| Oct.6  | Apple  |
| Oct.6  | Berry  |
| Oct.6  | Cherry |
| Oct.7  | Cherry |

</div> <div style="display: inline-block">

| Fruit  | Price |
| :----: | :---: |
| Apple  |  6$   |
| Berry  |  6$   |
| Cherry |  7$   |

</div></div>

## 3NF

满足2NF，不允许任何非主字段传递函数依赖于候选键。

这是一个不满足3NF但满足2NF的表：

| Index（候选键） | Fruit（非主字段） | Price（非主字段） |
| :-------------: | :---------------: | :---------------: |
|        1        |       Apple       |        1$         |
|        2        |      Banana       |        2$         |
|        3        |       Berry       |        6$         |
|        4        |       Berry       |        6$         |
|        5        |      Cherry       |        7$         |

它存在这样的关系：

$\rm Index→Fruit→Price,Fruit\nrightarrow Index,Price⊊Fruit⊊Index$，即传递函数依赖 $\rm Index\stackrel{T}{\longrightarrow}Price$ 。

经模式分解后得到满足3NF的表：

<div><div style="display: inline-block">

| Index | Fruit  |
| :---: | :----: |
|   1   | Apple  |
|   2   | Banana |
|   3   | Berry  |
|   4   | Berry  |
|   5   | Cherry |

</div> <div style="display: inline-block">

| Index | Price |
| :---: | :---: |
|   1   |  1$   |
|   2   |  2$   |
|   3   |  6$   |
|   4   |  6$   |
|   5   |  7$   |

</div></div>

1NF、2NF、3NF是生产环境中常见的范式级别。

## BCNF（Boyce Codd Normal Form）

满足3NF，不允许任何字段完全函数依赖于不能构成候选键的字段集合。

| Index（候选键） | Ranking（非主字段） | Fruit（非主字段） | Price（非主字段） |
| :-------------: | :-----------------: | :---------------: | :---------------: |
|        1        |          A          |       Apple       |        1$         |
|        2        |          B          |      Banana       |        2$         |
|        3        |          C          |       Berry       |        6$         |
|        4        |          C          |       Berry       |        6$         |
|        5        |          D          |      Cherry       |        6$         |
|        6        |          C          |      Cherry       |        8$         |

上表中，$\rm\{Fruit,Price\}\stackrel{F}\longrightarrow Ranking$，因而不满足BCNF。

经模式分解后满足BCNF：

<div><div style="display: inline-block">

| Index | Ranking |
| :---: | :-----: |
|   1   |    A    |
|   2   |    B    |
|   3   |    C    |
|   4   |    C    |
|   5   |    D    |
|   6   |    C    |

</div> <div style="display: inline-block">

| Index | Fruit  | Price |
| :---: | :----: | :---: |
|   1   | Apple  |  1$   |
|   2   | Banana |  2$   |
|   3   | Berry  |  6$   |
|   4   | Berry  |  6$   |
|   5   | Cherry |  6$   |
|   6   | Cherry |  8$   |

</div></div>

## 4NF

满足BCNF，并消除多值依赖。

多值依赖（Multivalued Dependency）：对于 $X,Y,Z⊂U$，若 $(X,Y)→Z,X\rightarrow Z,Y\nrightarrow Z$，则称 $Z$多值依赖于$X$，记作 $X→→Z$ 。  
平凡多值依赖：$X→→Z$ 且 $Y=Ø$ 。  
非平凡多值依赖：$X→→Z$ 且 $Y≠Ø$ 。

对于下表，**规定**水果的价格仅与Index有关而与种类无关：

| Index | Fruit  | Price |
| :---: | :----: | :---: |
|   1   | Apple  |  1$   |
|   2   | Banana |  2$   |
|   3   | Berry  |  6$   |
|   4   | Berry  |  6$   |
|   5   | Cherry |  6$   |
|   6   | Cherry |  8$   |

则存在多值依赖 $\rm Index→→Price$，这不满足4NF。

<div><div style="display: inline-block">

| Index | Price |
| :---: | :---: |
|   1   |  1$   |
|   2   |  2$   |
|   3   |  6$   |
|   4   |  6$   |
|   5   |  6$   |
|   6   |  8$   |

</div> <div style="display: inline-block">

| Index | Fruit  |
| :---: | :----: |
|   1   | Apple  |
|   2   | Banana |
|   3   | Berry  |
|   4   | Berry  |
|   5   | Cherry |
|   6   | Cherry |

</div></div>

现满足4NF。

## 5NF

满足4NF，将表拆分到最简形式。

对4NF案例中的最后结果进行5NF范式化如下：

<div><div style="display: inline-block">

| Index | Fruit  |
| :---: | :----: |
|   1   | Apple  |
|   2   | Banana |
|   3   | Berry  |
|   4   | Berry  |
|   5   | Cherry |
|   6   | Cherry |

</div> <div style="display: inline-block">

| Index | Price |
| :---: | :---: |
|   1   |  1$   |
|   2   |  2$   |
|   3   |  6$   |
|   4   |  6$   |
|   5   |  6$   |
|   6   |  8$   |

</div> <div style="display: inline-block">

| Fruit  | Price |
| :----: | :---: |
| Apple  |  1$   |
| Banana |  2$   |
| Berry  |  6$   |
| Berry  |  6$   |
| Cherry |  6$   |
| Cherry |  8$   |

</div></div>

---

### 注解

1. 这不是严格的（注意到用语「潜在的」），一味地追求符合范式的库表结构很可能带来严重的性能缺陷。例如满足5NF的库表设计通常会使用大量的关联查询，很显然这类开销是不必要的（通常也很少见到满足5NF设计的库表结构）。

   关于二者的比较（通常情况下）：
    |    \     | 范式化 | 反范式化 |
    | :------: | :----: | :------: |
    | 更新速度 |   +    |    -     |
    | 数据冗余 |   -    |    +     |
    | 关联成本 |   +    |    -     |
    | 索引利用 |   -    |    +     |

2. 对于理解传递函数依赖的条件：  
    $Z⊄Y,Y⊄X$：对于任一字段 $X$，$X→X$ 是显然的。若 $Y⊂X$，那么 $X\stackrel{P}\longrightarrow Y$ 更是显然的，故要求 $Z⊄Y,Y⊄X$ 以保证 $X\stackrel{P}\longrightarrow Z$ 不成立。  
    $Y\nrightarrow X$：对于 $X→Y$，若 $Y→X$，那么 $X⇔Y$ 是显然的，因而在没有 $Y→Z$ 的“传递”时， $X\stackrel{T}\longrightarrow Z$ 也能成立。换句话来说，如果 $X→Y,Y→X$，那么传递是冗余的，$X$和$Y$中的一者也是冗余的。某些出处可能将这种情况下的函数依赖归为“直接函数依赖”。
3. 字段=属性，键=码。我个人认为属性和码的叫法已经过时了，在不与现代的数据库名词对应的同时也易产生混淆。
4. 因为存在同一生产日期（Oct.6）有不同的水果，所以生产日期（DP）不能成为候选键。  
   因为存在同一水果（Cherry）有不同的生产日期，所以水果（Fruit）不能成为候选键。  
   因为存在同一价格（6\$）有不同的水果，所以价格（Price）不能成为候选键。
5. 对于 $\rm\{DP,Price\}$，其反例是(Oct.6, 6\$)。  
   对于 $\rm\{Fruit,Price\}$，其反例是(Cherry, 7$)。
6. 将原有的关系模式拆分，这种拆分的方式具有多样性，且要求拆分不损失原有的关系（即拆分后能够恢复到原有的关系，这被称作**无损连接**）。
