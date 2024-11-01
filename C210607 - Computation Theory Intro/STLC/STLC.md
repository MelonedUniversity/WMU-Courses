# Simply Typed Lambda Calculus

简单类型λ演算

## 概要

**简单类型λ演算**（$λ\scriptsize{→}$）是一种有类型的λ演算，它通过在λ演算上引入一个基本的类型系统来实现对类型的约束。本文仅概要性地介绍*Church风格*$^{*1}$下原始意义上的$λ\scriptsize{→}$。

## $λ\scriptsize{→}$中的类型系统

$λ\scriptsize{→}$中的类型构造自形如 $α\ β\ γ...$ 的基本类型，如 $α→β$表示函数类型。其中，$→$ 是右结合的，这意味着 $α→β→γ:=α→(β→γ)$ $^{*2}$。

通过介入类型上下文（以下简称“上下文”） $Γ\ Δ...$，我们可以按照如下规则确定一个 $λ$ 项的类型：

1. ${}\over \displaystyle {x:σ\ \vdash\ x:σ}$，即如果 $x$ 为 $σ$ 类型，那么我们可以知道 $x$ 为 $σ$ 类型（对同一律抽象）。

2. ${\displaystyle Γ\ \vdash\ x:σ\quad x\not=y}\over \displaystyle {Γ,y:τ\ \vdash\ x : σ}$，即在上下文 $Γ$ 中，$x$ 有 $σ$ 类型，且 $x≠y$，那么可以推导出：在上下文 $Γ$ 中，$y$ 有 $τ$ 类型，可以推导出 $x$ 有 $σ$ 类型。即向 $Γ$ 中新增具有某类型的值并不影响原有值的类型。

3. ${\displaystyle Γ,x:σ\ \vdash\ t:τ}\over \displaystyle {Γ\ \vdash\ λx:σ.\ t : σ \to τ}$，即如果由上下文 $Γ$ 和 $x$ 具有类型 $σ$ 的事实可以推出 $t$ 具有 $τ$ 类型，那么在上下文 $Γ$ 中可以抽象出具有 $σ→τ$ 类型的函数 $λx.\ t$。

4. ${\displaystyle Γ\ \vdash\ t:σ\toτ\quadΓ\ \vdash\ u:σ}\over \displaystyle{Γ\ \vdash\ t\,u : τ}$，即函数应用：上下文 $Γ$ 中有 $σ→τ$ 的 $t$ 和 $σ$ 类型的 $u$，可以推导出上下文 $Γ$ 中的 $t\ u$ 为 $τ$ 类型。

## 关于正则化

如果一个 $λ$ 项，如果它不能被进一步地 $β$ 归约，那么它就是正则的（Normalized）。不是所有的 $λ$ 演算都具有这种性质，因为它存在可被无穷 $β$ 规约的项（$λx.x\ x$ 就是一个例子）。而在 $λ\scriptsize{→}$ 中，项要么是正则的，要么是可以被化为正则的（即可正则化（Normalizable）的）。

### $λ\scriptsize{→}$是强正则的

类型系统的引入使得 $λ\scriptsize{→}$ 的所有 $λ$ 项都是**强正则**（Strongly Normalized）的，这意味着不存在一个 $λ$ 项使得其可以被无穷地 $β$ 归约，因为你无法找到这样的 $λ$ 项所对应的类型。同时这也是**强正则定理**（Strong Normalization Theorem）的表述：任何 $λ$ 项都能经过有限次 $β$ 归约化为正则项，如果 $λ$ 项是有类型的。

### $λ\scriptsize{→}$也是弱正则的

**弱正则定理**（Weak Normalization Theorem）：对于一个有类型的 $λ$ 项，其可被正则化。

显然，弱正则定理是强正则定理的推论，这也解释了为什么 $λ\scriptsize{→}$ 具有*可计算性*$^{*3}$。

## 关于$λ\scriptsize{→}$的表达

通过引入类型系统，我们现在可以*这样*$^{*4}$构造自然数：

$0:=λf:α→α.\ λx:α.\ x$  
$1:=λf:α→α.\ λx:α.\ f\ x$  
$2:=λf:α→α.\ λx:α.\ f\ (f\ x)$  
$...$  
$n:=λf:α→α.\ λx:α.\ f^{○n}\ x$

现在，我们可以规定自然数的类型了（受限于自然数的特点，这有些牵强）：

$1_{int}:=λf:int→int.\ λx:int.\ f\ x$  
$2_{float}:=λf:float→float.\ λx:float.\ f\ (f\ x)$  

和布尔值：

$true\ \ :=λa:α.\ λb:α.\ a$  
$false:=λa:α.\ λb:α.\ b$

还记得之前的 $if$ 吗？现在它的两个分支必须是相同类型的了：

$if:=λp:α.\ λa:α.\ λb:α.\ p\ a\ b$

注意这里所有的 $α$ 都是**具体类型**，它并不是类型参数。也就是说形如 $λf:α→α.\ λx:α.\ x$ 的项是一个**值**，而非**值构造器**。

## In λ Cube

$λ$ 立方体（Lambda Cube）描述了不同类型系统之间的关系，自左下角至右上角，类型系统的表达能力逐渐增强。

<img style="background:white;" src="res/lambda_cube.svg" width="256px">

在λ立方体中，$λ\scriptsize{→}$ 居于最左下角，具有最基本（也是最弱）的表达能力。

|     系统\特征     |   $→$   |  $Π$  |  $ω$  |  $2$  |
| :---------------: | :-----: | :---: | :---: | :---: |
| $λ\scriptsize{→}$ | $(*,*)$ |   ╳   |   ╳   |   ╳   |

$(*,*)$ 表示 $λ\scriptsize{→}$ 允许由项构造项（函数），这也是其唯一具有的特征。

---

### 注解

1. 另有Curry风格的$λ\scriptsize{→}$，其特征是不在变量上标注类型。
2. $a:=b$ 表示可以将 $a$ 代换为 $b$，即它们是**外延相等**的。
3. 即可以在有限步骤内被解决。显然在 $λ\scriptsize{→}$ 中所有的 $λ$ 项都能在有限次的 $β$ 规约中化为正则项。
4. 使用Church Encoding。
