# Functor

函子

> 为阅读本节，你需要掌握：Kotlin入门

## 概要

**函子**是对一类容器的抽象，该类容器存储一个值，并且对于这个值，该容器规定了它的变换方式。

## 引导

在实践中，我们经常会遇到需要包装一个值的情况，比如：

```Kotlin
class Box(val value: Int)
```

这里有一个`Box`类型，它存储一个`Int`类型的值。  
但仅这样并不够，因为通常我们需要多种类型的`Box`来装不同的东西。现对`Box`容器进行改造：

```Kotlin
class Box<A>(val value: A)
```

现在`Box`可以装任何类型的东西了。  
现在我们想改变`Box`内部的值（这包括类型），考虑到避免使用变量可能带来副作用，所以对于`Box`，我们给出一个高阶函数`fmap`用于产生一个新的`Box`来实现以上需求：

```Kotlin
class Box<A>(val value: A) {
    fun <B> fmap(f: (A) -> B) = Box<B>(f(value))
}
```

有了`fmap`，我们只需要传入函数`f`（它规定了如何将`A`类型转换为`B`类型），我们就能实现任意`Box<A>`到`Box<B>`的变换。

## 函子

考虑到这种容器十分普遍，所以我们抽象出这类容器的特征，这便是函子的接口表述：

```Kotlin
/* 由于Kotlin不支持高阶类型，于是我们构造了一个Kind来模拟。
 此处，Kind<T,V>表示一个具有 一个 类型参数V的类型T */
interface Kind<T, V>
//此处的泛型约束保证了T是有 一个 类型参数的类型
interface Functor<T : Kind<T, *>> {
    fun <A, B> Kind<T, A>.fmap(f: (A) -> B): Kind<T, B>
}
```

`Box`现在实现`Functor`接口：

```Kotlin
object WithBoxFunctor : BoxFunctor<Any>
class Box<V>(val value: V) : BoxFunctor<V>, Kind<BoxFunctor<*>, V>

@Suppress("unchecked_cast")
interface BoxFunctor<V> : Functor<BoxFunctor<*>>, Kind<BoxFunctor<*>, V> {
    override fun <A, B> Kind<BoxFunctor<*>, A>.fmap(f: (A) -> B): Kind<BoxFunctor<*>, B> {
        val fa = this as Box<A>
        val fb = Box<B>(f(fa.value))
        return fb as Kind<BoxFunctor<*>, B>
    }
}
```

其中，单例`WithBoxFunctor`用于导出`BoxFunctor`中的`fmap`方法（通过`WithBoxFunctor.run{}`）以允许`Box<V>`类型调用`fmap`。而`BoxFunctor<V>`是对所有符合Functor特征的`Box<V>`的统一抽象（因为**不管Box这个容器装的是什么**，它都是Functor），类型参数`V`其实是为了满足`Functor<T>`的泛型约束`T:Kind<T,*>`而添加的，即`V`是一个**幻影类型**（Phantom Type）。

虽然这里使用了类型检查屏蔽注解，但由于`fmap`是在`Kind<BoxFunctor<*>, A>`上拓展的，而`Box<V>`恰好就是`Kind<BoxFunctor<*>, A>`，所以仍然是类型安全的。

让我们测试一下：

```Kotlin
val B1 = Box<Int>(1)
val result = WithBoxFunctor.run {
    B1.fmap { x -> "Result is ${x + 1}" }
}
println((result as Box).value)//Result is 2
```

Awesome，`Box<Int>`成功地被变换为了`Box<String>`。

---

### FAQ

* A：为什么在众多语言中选用Kotlin来表述用例？
* Q：出于易于理解的考虑，本文最初打算使用Java来编写，但由于Java类型擦除式的泛型实现，我对于严格约束的高阶类型和Typeclass始终没有优雅的解决方案。随后我选用了C#进行改写，但很快发现C#对于泛型的约束过于严格（没有类型通配）而难以优雅地实现对instance的抽象，而且C#的拓展方法语法过于繁琐复杂（且无法实现静态扩展！），于是就选用了在以上特性都非常完善的Kotlin。

* A：为什么不借助Kotlin的类型推导而显示地声明类型参数？
* Q：出于易于理解的考虑，本文会对一些可能难以一步看出的类型显示地声明类型参数，同时部分地借用类型推导功能以方便读者理解哪些类型参数在此处是不必要的。
