# Generalised Algebraic Data Type

广义代数数据类型

## 概要

**广义代数数据类型**（又称一般化的代数数据类型）是ADT的增强版本，它允许在ADT的基础上通过为**值构造器**$^{*1}$（Value constructor或Data constructor）提供更加丰富的类型信息来完善类型约束，下文简称GADT。

## 介绍

虽然ADT具有易于使用和类型安全等一系列优异特性，但它仍然存在一定不足，考虑以下代码：

```Haskell
data Expr
  = BoolVal Bool
  | IntVal Int
  | Sum Expr Expr
  | Equal Expr Expr
```

上述代码构造了一个表达式类型`Expr`，其含有代表Bool类型表达式的值构造器`BoolVal`和代表Int类型表达式的值构造器`IntVal`，以及分别代表求和表达式和判等表达式的`Sum`和`Equal`。

我们可以很容易的实现对以上`Expr`的解析，这在构建DSL$^{*2}$时很有用：

```Haskell
eval :: Expr -> Either Bool Int
eval (BoolVal b) = Left b
eval (IntVal i) = Right i
eval (Add e1 e2) =
  case eval e1 of
    Right i1 -> case eval e2 of Right i2 -> Right (i1 + i2)
eval (Equal e1 e2) =
  case eval e1 of
    Left b1 -> case eval e2 of Left b2 -> Left (b1 == b2)
    Right i1 -> case eval e2 of Right i2 -> Left (i1 == i2)
```

受限于篇幅和可读性缘故，此处的`eval`是一个非穷尽（non-exhaustive）的模式匹配。由于`Sum`和`Equal`有很多种模式需要考虑，为其编写的冗余代码不但无助于逻辑本身，反而去掉会使得程序不安全：

```Haskell
foo = eval (Add (IntVal 1) (BoolVal False)) -- “合法的滥用”
```

如果对ADT加以约束，那么上面所述的一些问题则不会发生，这就是GADT：

```Haskell
data Expr a where
  BoolVal :: Bool -> Expr Bool
  IntVal :: Int -> Expr Int
  Add :: (Expr Int) -> (Expr Int) -> Expr Int
  Equal :: (Expr a) -> (Expr a) -> Expr Bool

eval :: Expr a -> Either Bool Int
eval (BoolVal b) = Left b
eval (IntVal i) = Right i
eval (Add (IntVal i1) (IntVal i2)) = Right (i1 + i2)
eval (Equal (BoolVal b1) (BoolVal b2)) = Left (b1 == b2)
eval (Equal (IntVal i1) (IntVal i2)) = Left (i1 == i2)
```

可以看到通过使用GADT，我们不需要考虑那些滥用情况下的模式，因为它们都被GADT约束掉了。这大大减少了代码冗余，也更可读$^{*3}$。同时，我们在类型层面为`eval`带来了类型安全性，而不是手工地去写全所有情况的匹配。

---

### 注解

1. 值构造器是类型构造器的一部分，如有以下类型：

   ```Haskell
    data Type a = Foo a | Bar a | Baz
   ```

   `Foo`、`Bar`和`Baz`都是值构造器，更由于`Baz`无类型参数，故被称作空的值构造器（Nullary data constructor）或常量（Constant）。

2. 领域特定语言（Domain Sepcific Language，DSL），如HTML、SQL等。
3. 对于可读性的讨论，这里其实并不严谨，因为两个例子使用了不同的模式匹配方式。但基于可读性和代码量成负相关的事实，我姑且认为这样的表述还算可用。
