# Message Passing

消息传递

## 概要

**消息传递**是使用过程构造数据的一种方式。

## 一般的数据构造

我们通常把数据组织在某种结构里，例如这个序对$^{*1}$的构造（函数）：

```Kotlin
data class Cons<T>(val car: T, val cdr: T)
```

为了取得序对的头和尾，我们可以采用以下*选择函数*$^{*2}$：

```Kotlin
fun <T> car(t: Cons<T>) = t.car
fun <T> cdr(t: Cons<T>) = t.cdr
```

如果有多种数据结构都支持`car`和`cdr`的抽象，很自然的想法是为`car`和`cdr`提供多种重载，来实现对其他数据结构的访问。

## 消息传递

在上述案例中，对数据结构访问的函数类似于一种“智能操作”，它智能地匹配类型，进而应用相应的操作。而消息传递则构造了一种“智能对象”，这个对象本身是一种函数，它通过识别操作的类型来进行相应的操作$^{*3}$：

```Kotlin
fun <T> makeCons(car: T, cdr: T) = { x: String ->
    when (x) {
        "car" -> car
        "cdr" -> cdr
        else -> throw Exception("err")
    }
}
```

于是我们便可如下地取得序对的值：

```Kotlin
fun main() {
    val cons = makeCons(22, 33)
    println(cons("car"))//22
    println(cons("cdr"))//33
}
```

消息传递也是一种函数式编程思想，它的本质实际上就是柯里化（Currying）。

---

### 注解

1. 这里的序对是退化的，它不能方便地表示递归式列表或者树，因而可以将它简单地看成元组。当然可以使用ADT让这里的序对一般化（例如允许它递归地构造列表），但那样会使得消息传递的例子难以编写（这需要弱类型，这也正是消息传递为什么能够在Lisp中应用广泛的原因之一）。

2. 与构造函数相对应，用于解构数据。

3. 用字符串充当操作名是不优雅的（同时也不类型安全），正确的做法是让操作符号化（例如采用枚举）。这里这样做仅是为了避免引入过多噪音，让例子显得易懂些。