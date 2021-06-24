# Applicative Functor

应用函子

## 概要

**应用函子**是函子的增强版本，它允许被容器包装的函数对容器的值进行变换，并新增了一个用于包装值的函数。

## 接口表述

所有的应用函子都是函子，也就是说，一个容器要想成为应用函子，那么它首先需要成为函子。我们使用如下接口构造应用函子：

```Kotlin
interface Applicative<T : Kind<T, *>> : Functor<T> {
    override fun <A, B> Kind<T, A>.fmap(f: (A) -> B): Kind<T, B> = this.ap(f.pure())

    fun <A> A.pure(): Kind<T, A>
    fun <A, B> Kind<T, A>.ap(af: Kind<T, (A) -> B>): Kind<T, B>
}
```

其中，`pure`函数用于把一个`A`类型的值包装成`T<A>`类型的值，`ap`函数接收一个包装在容器`T`中的函数，并通过该函数将`T<A>`变换到`T<B>`。一旦有了`pure`和`ap`，那么`fmap`就是不必要的了，因为`pure`和`ap`能构造出`fmap`。可见，应用函子是函子的增强。

回到之前`Box<V>`的例子，`BoxApplicative<V>`就可以抽象如下：

```Kotlin
@Suppress("unchecked_cast")
interface BoxApplicative<V> : Applicative<BoxApplicative<*>>, Kind<BoxApplicative<*>, V> {
    override fun <A> A.pure(): Kind<BoxApplicative<*>, A> = Box<A>(this) as Kind<BoxApplicative<*>, A>

    override fun <A, B> Kind<BoxApplicative<*>, A>.ap(af: Kind<BoxApplicative<*>, (A) -> B>): Kind<BoxApplicative<*>, B> {
        val a = (this as Box<A>).value
        val f = (af as Box<(A) -> B>).value
        return Box<B>(f(a)) as Kind<BoxApplicative<*>, B>
    }
}
```

由于应用函子都是函子，所以`Box<V>`继承`BoxApplicative<V>`后仍是函子：

```Kotlin
object WithBoxApplicative : BoxApplicative<Any>
class Box<V>(val value: V) : BoxApplicative<V>, Kind<BoxApplicative<*>, V>
```

现在就可以把函数包装在`Box`容器内进行链式调用了：

```Kotlin
val B2 = Box<(Int) -> String> { x -> "Result is ${x + 1}" }
val B3 = Box<(String) -> String> { x -> "Hey! $x" }

val result = WithBoxApplicative.run {
    1.pure().ap(B2).ap(B3)
}
println((result as Box).value)//Hey! Result is 2
```

不过受限于Kotlin的函数不能Currying，这里的Applicative并不能实现更复杂的函数组合（Functor也是如此）。

## Playground

没有作业
