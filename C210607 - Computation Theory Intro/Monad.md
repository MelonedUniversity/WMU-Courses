# Monad

单子

## 概要

自函子范畴上的幺半群（逃

## 什么是Monad

如果一个应用函子拥有`unit`函数和`bind`函数，那么它就是**单子**（Monad）。所有的Monad都是应用函子。

如果你已经了解函子和应用函子，那么理解Monad其实并不困难：

```Kotlin
@Suppress("unchecked_cast")
interface Monad<T : Kind<T, *>> : Applicative<T> {
    override fun <A> A.pure(): Kind<T, A> = this.unit()
    override fun <A, B> Kind<T, A>.ap(af: Kind<T, (A) -> B>): Kind<T, B> = this.ap(af)

    fun <A> A.unit(): Kind<T, A> = pure()

    fun <A, B> Kind<T, A>.bind(mf: (A) -> Kind<T, B>): Kind<T, B>
}
```

`unit`用于包装一个值，其实它等效于`pure`，只是名称不同而已。  
`bind`接收一个函数，与之前不同的是，该函数接收一个未被包装的值，并返回一个被包装的值。

另外，所有的Monad都具有一个奇妙的特性：把函数通过`bind`应用到Monad的结果总是等于把函数通过`fmap`应用到Monad然后将结果展平(`join`)（因为结果是一个嵌套的Monad）：

```Kotlin
fun <A> Kind<T, Kind<T, A>>.join(): Kind<T, A>

fun <A, B> Kind<T, A>.bind(mf: (A) -> Kind<T, B>): Kind<T, B> = this.fmap(mf).join()
```

这一特性可以帮助我们在不知道如何实现`bind`的情况下间接的实现它，因为通常很容易指出如何展平一个Monad。

## 单子定律

如果实现以上接口，那么所有的Monad都满足以下定律：

* 左幺元

    ```Kotlin
    m.unit().bind(mf) == mf(m)
    ```

* 右幺元
  
    ```Kotlin
    m.bind{ x -> x.unit() } == m
    ```

* 结合律

    ```Kotlin
    (m.bind(mf1)).bind(mf2) == m.bind{ x -> mf1(x).bind(mf2) }
    ```

可以发现，Monad具有Monoid的一些特征。  
其实，迄今我们讨论的Functor都是自函子，自然Monad也是。  
自然地，Monad就是自函子范畴上的幺半群了（是的，这是句废话233

## 应用

Monad这类代数结构的独特特点决定了它很适合抽象副作用，通过使用Monad，我们能将副作用隔离在Monad上下文中，从而最大限度地避免其对程序的其他部分产生不良影响。

Maybe是一个典型的Monad，下面我们以Maybe为例看看Monad的应用$^{*1}$:

```Kotlin
@Suppress("unchecked_cast")
interface MaybeMonad<V> : Monad<MaybeMonad<*>>, Kind<MaybeMonad<*>, V> {

    override fun <A> A.unit(): Kind<MaybeMonad<*>, A> = Just<A>(this)

    override fun <A, B> Kind<MaybeMonad<*>, A>.bind(mf: (A) -> Kind<MaybeMonad<*>, B>): Kind<MaybeMonad<*>, B> {
        val mb = when (val ma = this) {
            is Just<A> -> mf(ma.value)
            is Nothing -> Nothing<B>()
            else -> Nothing<B>()
        }
        return mb as Kind<MaybeMonad<*>, B>
    }
}

object WithMaybeMonad : MaybeMonad<Any>
sealed class Maybe<V> : MaybeMonad<V>, Kind<MaybeMonad<*>, V> {
    class Just<V>(val value: V) : Maybe<V>()
    class Nothing<V> : Maybe<V>()
}
```

`Maybe<V>`类型有两种，`Just<V>`反映了正确的结果，而`Nothing<V>`则代表出现了错误。

假设现在你开发的程序从某用户那里得到一串数字输入，该程序计算它们的和。但该用户在这串输入中不小心夹杂了一个字符串（会导致运算错误！），显然你不希望用户在这个情境中收到一串错误代码或是发现程序崩溃了，那么你就可以使用`Maybe<Sum>`来表达这种可能发生错误的计算结果$^{*2}$：

```Kotlin
    val J1 = Just<Sum>(Sum(1))
    val J2 = Just<Sum>(Sum(2))
    val ERR = Nothing<Sum>()
    val J3 = Just<Sum>(Sum(3))

    val succResult = WithMaybeMonad.run {//正确的计算流
        J1.bind { x: Sum -> J2.bind { y: Sum -> J3.bind { z: Sum -> Just<Sum>(x.mappend(y.mappend(z))) } } }
    }
    val errResult = WithMaybeMonad.run {//发生错误的计算流
        J1.bind { x: Sum -> ERR.bind { y: Sum -> J3.bind { z: Sum -> Just<Sum>(x.mappend(y.mappend(z))) } } }
    }

    when (succResult) {//6
        is Just<Sum> -> println(succResult.value.value)
        is Nothing -> println("Noting")
    }
    when (errResult) {//Nothing
        is Just<Sum> -> println(errResult.value.value)
        is Nothing<Sum> -> println("Noting")
    }
```

如此，错误就完全由Maybe进行托管了，你不需要关注哪一部分出现了错误（并加上相应的错误处理逻辑或是设定一个特殊的值来表示错误），而只需要在最终取用计算结果的时候对其进行判断即可。

你可能会说，我一开始判断用户输入是否合法不就行了吗？上述讨论的其实是一种函数式编程的思维方式，函数式编程使用类型来代替命令式编程中异常处理(即`try...catch...`块)，这种方式的好处是能够始终把错误隔离在类型中，从而防止错误的影响传播到其他地方（比如让程序崩溃）。这样的方式在命令式编程中是存在的（比如Java8引入的`Optional<T>`就是为了用类型代替错误的），但尚未得到广泛应用。

## Playground

没作业。

### 注解

1. `Maybe<V>`仍满足单子定律，读者可以自行验证。
2. 幺半群`Sum`出现在Monoid那节中，由于当时使用Java表述，现给出其Kotlin表述：

    ```Kotlin
    interface Semigroup<T> {
        fun sappend(sb: T): T
    }
    interface Monoid<T> : Semigroup<T> {
        fun mempty(): T

        fun mappend(mb: T): T {
            val ma = this
            return ma.sappend(mb)
        }
    }

    abstract class SumMonoid : Monoid<Sum> {
        override fun sappend(sb: Sum): Sum {
            val sa = this as Sum
            return Sum(sa.value + sb.value)
        }

        override fun mempty(): Sum = Sum(0)
    }
    class Sum(val value: Int) : SumMonoid
    ```
