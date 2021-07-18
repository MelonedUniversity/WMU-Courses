# Algebraic Data Type

代数数据类型

## 概要

**代数数据类型**是一种**复合类型**（Composite type），这种数据类型允许你以代数的形式将所需要的数据类型表示出来，在函数式编程中有着广泛的应用。

## 何为代数

代数在数学中无处不在，你一定对它非常熟悉，只不过出现在这里让你感到困惑罢了。代数就是符号化，通过把一些具体的数字以字母或符号代替，来表达更为普遍的或更加抽象的关系，这里沿用了数学上的概念。

对于代数，我们只关心它们的性质或彼此之间的关系，而并不关心它们具体是什么。

正是因为代数这种抽象十分重要，越来越多的编程语言提供了对代数的抽象，这种抽象的实际体现便是代数数据类型（此后简称ADT）。

## 和类型和积类型

**和类型**（Sum type）和**积类型**（Product type）是ADT的两大组成，要了解ADT就要先理解这两种类型。

### 枚举是和

比如C语言中的枚举类型，就是和类型：

```C
enum Alphabet {A, B, C};
Alphabet foo = B;//foo只能是A, B, C中之一
```

之所以称之为和类型，是因为其取值类型可以用求和的方式得出，比如`Alphabet`类型的取值有3种，这是通过求和得出的。

我们再来考察和类型的另一种表现形式：

```Java
class Alphabet {}
class A extends Alphabet {}
class B extends Alphabet {}
class C extends Alphabet {}
Alphabet foo = new B();//foo只能是A, B, C中之一
```

这里更多地强调的是一种子类和父类的派生关系，而不是实现上的复用，是*子类型化*（Subtyping）的一种体现。

### 结构体是积

C语言中的结构体是一种积类型：

```C
struct Bar {
    int a;
    int b;
};
struct Bar foo;
foo.a = 1;
foo.b = 2;
```

之所以称之为积类型，是因为其的取值范围是各组成部分取值范围之积，本例中的`Bar`类型的取值数是`a`和`b`的取值数的乘积。

## ADT

ADT是由和类型和积类型组成的复合类型，我们可以拿ADT来作很多事情，比如实现一个二叉树$^{*1}$：

```Java
class BinTree {}
class Empty extends BinTree {}//空节点
class Node extends BinTree {//非空节点
    int value;//节点值
    BinTree L;//左树
    BinTree R;//右树
}
```

然后我们就可以如此构造一棵二叉树了（可能有点难看）：

```Java
BinTree t = new Node() {{
    value = 1;
    L = new Node() 
        {{value = 2; 
        L = new Empty();R = new Empty();}};
    R = new Node() 
        {{value = 3; 
        L = new Empty();R = new Empty();}};
}};
```

Haskell对ADT的支持则更完善一些，实现起来也更加优雅：

```Haskell
data BinTree = Empty | Node BinTree Int BinTree
t = Node (Node Empty 2 Empty) 1 (Node Empty 3 Empty)
```

对ADT具有良好支持的语言不仅仅可以享受到构造ADT的精简语法，同时还能受益于类型安全带来的一系列优点。

## Playground

没作业。

---

### 注解

1. 在Java15中可以使用密封类来保证ADT的类型安全，这里没有演示。（本例中基类BinTree不是封闭的，这意味着它可能被非法派生。）
2. 可以简单地认为：子类型化是接口上的继承，而面向对象中的继承更多地强调实现上的复用（即“实现”上的继承）。
