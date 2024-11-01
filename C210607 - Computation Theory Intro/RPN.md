# Reverse Polish Notation

逆波兰表示法

## 概要

逆波兰表示法是一种将数学运算符后置的计算表达式，这种表示法省去了对括号的需要且算法实现极为简单，因而被广泛实现于商业计算器中。

## 问题引入

一般而言，我们的数学表达式会长成这个样子：$1+2+3$  

但如果遇到减法运算，那么就不得不考虑运算顺序问题，因为减法是不符合交换律的，即：$(1-2)-3≠1-(2-3)$。

相似的情况还有除法运算等。

为了正确表达这种计算顺序，我们引入了括号来表示运算优先级，但是括号有一个问题，那就是如果括号的嵌套非常多，那么可读性会极大地降低，例如：

$3-(1-(3-2)/3-(1+2))$

此表达式中的任何一对括号均不可或缺，否则计算顺序将被改变。为计算该表达式，你首先需要找到最高优先级的运算并计算出结果，如此下去，直到没有运算符为止。  

目前的情况还可以解决，但当括号数量达到成百上千时，你将难以找到括号的对应关系（相信大多富有编程经验的人都明白看到这样的括号会是什么感受：)))))))))))），而计算机在分析表达式时，将为多层级语法树结构的解析付出相当大的计算资源开销，显然在受限于成本的计算设备上这样的开销需求是不现实的。

## RPN

为解决该问题，波兰数学家扬·武卡谢维奇在1920年引入了逆波兰表示法（此后简称为RPN）。

在RPN中，运算符被后置，形如 `1-(2-3)` 的表达式在RPN中将被改写为 `1 2 3 - -` 。计算方法是：从左至右阅读RPN表达式，当遇到运算符时，取其左最邻参数与该运算符进行结合并计算，重复该过程直到表达式中无运算符为止。

于是我们先前的例子就被改写为：`3 1 3 2 - 3 / - 1 2 + - -`  
如果加上括号那么它就长这样：`3 ((1 ((3 2 -) 3 /) -) (1 2 +) -) -`（显然更加麻烦了，且不必要）  

该表示法最大的优点是可利用堆栈结构减少计算机内存访问，即解析计算表达式时，按照以下步骤（假定运算符的需要的操作数数量均为2）：

* 若遇到操作数，将操作数压入栈中。  
* 若遇到运算符，从栈顶弹出（pop）两个操作数与该运算符结合，并将计算结果压入栈中。

下面的Haskell代码演示了一个RPN解析器，它计算了我们上面讨论的RPN表达式：

```Haskell
parser "+" (x : y : s) = (y + x) : s
parser "-" (x : y : s) = (y - x) : s
parser "*" (x : y : s) = (y * x) : s
parser "/" (x : y : s) = (y / x) : s
parser " " s = s
parser x s = (read x) : s

calc e s = head $ foldl (\acc x -> parser x acc) s (words e)

main = do
  putStrLn . show . calc "3 1 3 2 - 3 / - 1 2 + - -" $ []
```

计算结果为 `5.333333333333333`，与预期完全一致。

显然你不需要理解上述代码是如何运行的，我写出这个例子只是为了说明RPN很容易被算法实现。
