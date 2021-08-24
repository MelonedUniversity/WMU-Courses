# Dependent Type

依赖类型

## 概要

**依赖类型**指的是自身类型依赖于某种类型的特定取值的类型。

## 什么是依赖类型

目标类型取决于某种类型的特定取值，满足这一特性的所有类型都可称作是依赖类型。

数组`int[114514]`就是一个依赖类型，类型`int[n]`的类型因`n`的取值而不同。注意`n`在这里是一个常量，即它是编译期决定的，我们把它称为编译时依赖类型，而如果是*VLA*$^{*1}$情况，则属于运行时依赖类型。

但注意`ArrayList<Integer>`不是依赖类型，因为`ArrayList<E>`依赖于类型而不是类型的特定取值，即它是`Type → Type`的，而不是`Term → Type`的。

某种程度上来讲，依赖类型很像是给一个集合标上了索引。

## 形式化表述

依赖数据类型主要有以下两种，它们分别是对积类型与和类型的更为一般的抽象。

### $Π$-类型

**$Π$-类型**（Pi-Type），又被称为依赖乘积类型或依赖函数类型，它是对多态函数的抽象。  
对于*全类*$^{*2}$ $\mathcal {U}$ 中的某一类型 $A:\mathcal {U}$，有一类型族 $B:A→\mathcal {U}$，则可称该类型族 $B$ 为依赖函数类型，因为其值域是随着参数而变化的（这意味着它没有一个固定的*陪域*$\ ^{*3}$），可记为：

&emsp;&emsp;$Π_{x:A}B(x)$

特别地，当 $B$ 为常值函数，依赖函数类型将退化为一般函数（乘积）类型，即类型 $Π_{x:A}B$ 与类型 $A→B$ 等价。  
例如，将自然数映射到实数的函数 $Π_{x:\mathbb{N} }\mathbb{R}$ 就是依赖函数类型的一个特例：函数的返回类型不因 $x$ 的类型而改变。

### $Σ$-类型

**$Σ$-类型**（Sigma-Type），又被称为依赖总和类型或依赖数对类型，它捕获了对有序数对的抽象。  
相似地，对于 $\mathcal {U}$ 中的某一类型 $A:\mathcal {U}$，有一类型族 $B:A→\mathcal {U}$，则可称该类型族 $B$ 为依赖总和类型，可记为：

&emsp;&emsp;$Σ_{x:A}B(x)$

$Π$-类型也可以表示为一个有序对 $(a,b):Σ_{x:A}B(x)$，$b$ 的类型依赖于 $a$ 的值，即若 $a:A$ 则 $b:B(a)$。  
特别地，当 $B$ 为常值函数，依赖总和类型将退化为一般乘积类型，即类型 $Σ_{x:A}B$ 与类型 $A→B$ 等价。

> 我个人认为，在某种意义上，$Π$-类型是对多态函数的抽象，而$Σ$-类型是对归纳数据类型（如Church Encoding下的自然数）的抽象。

## 语言支持性

由于依赖类型会给类型系统大大增加设计的复杂度$^{*4}$，所以多常见于面向证明（Proof Orinted）的语言中，如Agda、Coq、Idris、F*等。

## In λ Cube

在λ立方体中，依赖类型即为 $λ\small{Π}$，居于 $λ\scriptsize{→}$的右侧。

|  系统\特征   |   $→$   |   $Π$   |  $ω$  |  $2$  |
| :----------: | :-----: | :-----: | :---: | :---: |
| $λ\small{Π}$ | $(*,*)$ | $(*,□)$ |   ╳   |   ╳   |

$(*,□)$ 表示 $λ\small{Π}$ 允许由项构造类型。

---

## 注解

1. 变长数组（Variable Length Array，VLA）。
2. 由全部类型构成的类型，可理解为类型的全集。
3. 包含值域的任何集合。
4. > 由于确定两个依赖于值的类型的等价性需要涉及具体的计算，若允许在依赖类型中使用任意值的话，其类型检查将会成为不可判定问题；换言之，无法确保程序的类型检查一定会停机。一些以证明辅助为主要目的的编程语言采用强函数式编程（total functional programming），这消除了停机问题，同时也意味着通过它们自身的核心语言无法实现任意无限递归，不是图灵完全的，如 Coq 和 Agda；另外一些依赖类型编程语言则是图灵完全的，如 Idris、由 ML 派生而来的 ATS 和 由 F# 派生而来的 F*。————Wikipedia