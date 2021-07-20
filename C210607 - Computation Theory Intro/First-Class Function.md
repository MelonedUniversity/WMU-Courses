# First-Class Function

第一类函数

## 概要

**第一类函数**是编程语言的一种特性，它允许将函数作为变量声明、传递和返回等，就像对待普通变量一样。在第一类函数语言中，函数是头等公民，享有和其他类型一样的地位。

## 用高阶函数解决问题

如果你有这样一种需求：你厌烦于每次输出后都要打一个`\n`来换行。于是你实现了这样一个函数来节省工作量（尽管貌似并没有减少多少甚至增加了）：

```C
void printWithWarp(char *words) {
    printf("%s\n", words);
}
```

某一天你突然发现不仅是换行，你还重复着大量的句号的键入工作，于是你又写了另一个函数：

```C
void printWithPeriod(char *words) {
    printf("%s.", words);
}
```

但糟糕的是，你突然发现还需要感叹号的版本！哦天哪，你不能总是重复编写这样的函数，于是你编写了这样一个函数来替代以上所有版本：

```C
void printWith(char *words, char *end) {
    printf("%s%s", words, end);
}
```

现在你可以实现任意字符（串）的跟随输出了。

需求变化的很快，现在你需要根据业务逻辑来判断需要输出什么样的结尾，显然你不能每次都在需要输出的地方嵌入一大堆`if...else...`来改善你的生活。这时候一个会宏命令的程序员跳出来，帮你改了一下代码：

```C
#define OnePeriod() \
({char* tempFn() {return ".";} tempFn();})//可以在tempFn内添加对业务逻辑的判断
#define TwoPeriod() \
({char* tempFn() {return "..";} tempFn();})
#define ThreePeriod() \
({char* tempFn() {return "...";} tempFn();})

...

printWithWarp("hello!", OnePeriod());
printWithWarp("hello!", TwoPeriod());
printWithWarp("hello!", ThreePeriod());
```

现在你的代码得到了高度复用，实现了逻辑分离，这太棒了。

实际上这就是高阶函数的应用，但显然用C语言写起来并不优雅，因为宏命令无论是在可读性还是在易用性方面都没有原生语言来的方便，更何况很多文本编辑器对宏命令的高亮并不是那么友好。

## 函数式接口和 $λ$ 表达式

以Java语言为例，我们可以使用函数式接口和 $λ$ 表达式来简化以上语法：

```Java
...
public static void printWith(String words, Supplier<String> end) {
    System.out.println(words + end.get());
}
...
Supplier<String> end1=()->{return "."; };
Supplier<String> end2=()->{return "..";};

printWith("hello!",end1);
printWith("hello!",end2);
printWith("hello!",()->{return "...";});
printWith("hello!",()->{return "....";});
...
```

更进一步地，我们可以将函数式接口作为数据存储在任何我们想要的数据结构中。

## 第一类函数

第一类函数应支持以下特征：

* 函数能够作为变量传递（高阶函数）或返回（偏函数（Partial Function））
* 函数能够存储在数据结构中
* 能为函数指定名称

显然，Java虽然拥有第一类函数的特性（尽管是后来添加的），但其实现仍不优雅，我们来看看Haskell是怎么做的：

```Haskell
onePeriod p = if p then "." else "?"
twoPeriod p = ".."
threePeriod p = "..."

printWith words end = print $ words ++ end

main = do
  printWith "hello!" $ onePeriod True
  printWith "hello!" $ twoPeriod False
  printWith "hello!" $ (\p -> if p then "..." else "???") True
```

如果按照前面的写法来写就太简单了（甚至看起来和Python没什么区别），所以我增添了一个参数来充分体现Haskell的函数式语法。

## Playground

Nothing to play with.
