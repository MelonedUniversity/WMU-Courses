# Monoid

幺半群

## 概要

给定集合 $S$，其上有二元运算 $○$ 满足：$S○S→S$ 。若 $○$ 满足结合律，即：$∀x,y,z∈S$，有 $(x○y)○z=x○(y○z)$，则称有序对 $(S,○)$ 为半群(Semigroup)。若 $∃e∈S$ 且 $∀x∈S$，有 $x○e=e○x=x$，那么称三元组 $(S,○,e)$ 为幺半群(Monoid)。其中，$e$ 被称为幺元。

从数学角度理解Monoid是简单的，因为它只比Semigroup多了一个幺元 $e$ 。

## 举例

自然数集$N$和其上的运算 $+$ 和幺元 $0$ 构成的三元组 $(N,+,0)$ 是一个幺半群。  
自然数集$N$和其上的运算 $×$ 和幺元 $1$ 构成的三元组 $(N,×,1)$ 是一个幺半群。

可以想到，Monoid与Semigroup一样，是对一类代数结构的抽象。与程序设计中的 “接口” 这一概念相似。

## 编程语言表述

Monoid在编程中无处不在。

如果用Java表述，那么Monoid接口长这样：

```Java
interface Monoid<T> {
    Monoid<T> mempty();//幺元
    Monoid<T> mappend(Monoid<T> mb);//二元运算
}
```

下面我们来实现一个简陋的$^{*1}$`Maybe`类型，它代表可能发生错误，其值有两种：`Just value`表示没有错误（`value`表示正确的结果），`Nothing`表示有错误。

```Java
class MaybeSum implements Monoid<MaybeSum> {

    public Monoid<MaybeSum> mempty() {return new NothingSum();}

    public Monoid<MaybeSum> mappend(Monoid<MaybeSum> mb) {
        var ma = this;
        if (ma instanceof JustSum && mb instanceof JustSum)
            return new JustSum() {{value = ((JustSum) ma).value + ((JustSum) mb).value;}};
        else
            return new NothingSum();
    }
}

class JustSum extends MaybeSum {public int value;}
class NothingSum extends MaybeSum {}
```

MaybeSum满足结合律，读者可自行验证。

假设现在你开发的程序从某用户那里得到一串数字输入，该程序计算它们的和。但该用户在这串输入中不小心夹杂了一个字符串（会导致运算错误！），显然你不希望用户在这个情境中收到一串错误代码或是发现程序崩溃了，那么你就可以使用`MaybeSum`来表达这种可能发生错误的计算结果：

```Java
var resultA = M1.mappend(M2.mappend(M3.mappend(M4)));//JustSum value=10
var resultB = M1.mappend(M2.mappend(new NothingSum().mappend(M4)));//NothingSum
```

你可能会说，我一开始判断用户输入是否合法不就行了吗？其实这是一种函数式编程的思维方式，函数式编程使用类型来代替命令式编程中异常处理(即`try...catch...`块)，这种方式的好处是能够始终把错误隔离在类型中，从而防止错误的影响传播到其他地方（比如让程序崩溃）。

## Playground

$(N,-,0)$是一个幺半群吗？为什么？

### 注解

1. 因为符合Monoid特征的Maybe类型应该具有一个类型参数`A`，即`Maybe<A>`，`A`代表Maybe内的值，该值必须是一个幺半群。在本例中可看作`A`为Monoid$(N,+,0)$
