# Monoid

幺半群

## 概要

给定集合 $S$，其上有二元运算 $○$ 满足：$S○S→S$ 。若 $○$ 满足结合律，即：$∀x,y,z∈S$，有 $(x○y)○z=x○(y○z)$，则称有序对 $(S,○)$ 为半群（Semigroup）。若 $∃e∈S$ 且 $∀x∈S$，有 $x○e=e○x=x$，那么称三元组 $(S,○,e)$ 为**幺半群**（Monoid）。其中，$e$ 被称为**幺元**$^{*1}$（Identity）。

可见，幺半群比半群只多了一个幺元 $e$ 。

## 举例

自然数集$N$和其上的运算 $+$ 和幺元 $0$ 构成的三元组 $(N,+,0)$ 是一个幺半群。  
自然数集$N$和其上的运算 $×$ 和幺元 $1$ 构成的三元组 $(N,×,1)$ 是一个幺半群。

可以想到，Monoid与Semigroup一样，是对一类代数结构的抽象。与程序设计中的 “接口” 这一概念相似。

## 编程语言表述

Monoid这种代数结构无处不在，现在让我们从命令式语言的角度来看看Monoid。

如果用Java，那么Monoid接口长这样：

```Java
interface Monoid<T> {
    Monoid<T> mempty();//幺元
    Monoid<T> mappend(Monoid<T> mb);//二元运算
}
```

下面我们来实现一个`SumMonoid`类型，它描述了Sum的应有的特征，实质上是对Monoid$(N,+,0)$的抽象：

```Java
class SumMonoid implements Monoid<SumMonoid> {

    public Sum mempty() {
        return new Sum() {{value = 0;}};
    }
    
    public Sum mappend(Monoid<SumMonoid> mb) {
        var ma = this;
        return new Sum() {{value = ((Sum) ma).value + ((Sum) mb).value;}};
    }
}
```

有了`SumMonoid`，那么`Sum`就满足了成为`Monoid`的要求：

```Java
class Sum extends SumMonoid {public int value;}
```

可见，`Sum`满足结合律、左幺元和右幺元：

```Java
var S1 = new Sum() {{value = 1;}};
var S2 = new Sum() {{value = 2;}};
var S3 = new Sum() {{value = 3;}};


var expr1 = S1.mappend(S2.mappend(S3));//Sum value=6
var expr2 = S3.mappend(S1.mappend(S2));//Sum value=6

var Sum = new SumMonoid();
var le = Sum.mempty().mappend(S1);//Sum value=1
var re = S1.mappend(Sum.mempty());//Sum value=1
```

## Playground

$(N,-,0)$是一个幺半群吗？为什么？

### 注解

1. 其中，$e○x=x$ 中的 $e$ 被称作**左幺元**（Left identity），$x○e=x$ 中的 $e$ 被称作**右幺元**（Right identity）。
