# Continuation Passing Style

续体传递风格

## 概要

**续体传递风格**是一种编程风格，它通过显式地使用函数来接管上一函数的结果来实现控制流$^{*1}$。以下简称CPS。

## 应用序与正则序

在命令式编程中，语句的编写顺序即为控制流，比如：

```JavaScript
let a = (x) => x
let b = (x) => x
let c = (x) => x
console.log(a(1) + b(2) + c(3))//6
```

上述代码如何执行？当然是先计算`a`，再计算`b`，然后计算`c`，最后执行`console.log`。  
这种先计算再展开的求值策略（Evaluation Otrategy）被称为**应用序**（Applicative Order），它实现于绝大多数的编程语言中。

函数式语言普遍采用被称为**正则序**（Normal Order）的求值策略。在正则序中，表达式被先展开再求值，这就使得`console.log`会被放在第一位执行，然后再计算需要的数据。换句话说，不到万不得已，程序不会对任何表达式求值，这就是**惰性求值**（Lazy Evaluation）。

由于惰性求值这种不确定的求值顺序，如果按照常规方式（即控制流由语句编写顺序决定）构造控制流则很可能会导致执行顺序与预期不一致，于是就需要一种方法来约束表达式的求值顺序以实现正确的控制流，使用CPS就可以实现这个目的。

## CPS变换

CPS变换指的是将一般风格的控制流转换成CPS风格的控制流。

上文代码经CPS变换后如下：

```JavaScript
let a = (x, y) => y(x)//y is a callback function
let b = (x, y) => y(x)
let c = (x, y) => y(x)

console.log(
    a(1, a => 
    b(2, b => 
    c(3, c => a + b + c))))//6
```

在CPS风格下，函数具有一个回调函数（Callback Function），该回调函数负责接管当前函数的计算结果并将其传递至下一次计算。如果对上述代码进行展开，那么应该类似于这个样子（伪代码）：

```JavaScript
[a-> [b-> [c-> a+b+c ](3) ](2) ](1)
```

可以看到，在正则序下，求值顺序被约束到了函数的嵌套顺序中，从而避免了由于惰性求值导致的乱序执行问题。

由于CPS的写法比较难以阅读，所以有些基于正则序的语言提供了一些语法糖来帮助你编写CPS风格的代码，Haskell中的`do`表示法就是一个例子：

```Haskell
main = 
  return 1 >>= (\a -> 
  return 2 >>= (\b -> 
  return 3 >>= (\c -> 
  putStrLn . show $(a + b + c))))

main = do
  a <- return 1
  b <- return 2
  c <- return 3
  putStrLn . show $ a + b + c
```

两种写法是等价的。

## Playground

没作业。

---

### 注解

1. 计算机执行一个程序中语句的顺序。（引用自MDN）
