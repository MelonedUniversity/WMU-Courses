# Subtyping

子类型化

## 概要

**子类型化**是类型多态的一种形式。

## 介绍

如果子类型（subtype）$S$ 存在有一个超类型（supertype）$T$，那么可记Subtyping关系为 $S<:T$ 或 $T:>S$，这意味着所有有（或需要） $T$ 存在的地方都可以由 $S$ 安全地代换$^{*1}$。注意不要将Subtyping与面向对象中继承（inheritance）的概念相混淆，因为Subtyping关注的是类型间的关系（更类似于接口）而继承关注的是从现有对象创建新的对象，这意味着Subtyping和Inheritance是正交（orthogonal）的，尽管它们有重合的地方$^{*2}$。

类型（Type）是由含有一系列的值组成的集合（Set）。为了描述该集合，我们可以枚举地写出类型的所有取值，也可以使用谓词（Predicate）来过滤某个域（Domain）中的取值。

对于谓词表达的类型，我们认为谓词 $\textbf{S}$ 和谓词 $\textbf{T}$ 分别代表域 $D$ 上的相应类型，满足  $\textbf{S}$ 和 $\textbf{T}$ 的元素的集合分别记为 $S$ 和 $T$，对于同一域 $D$，Subtyping满足如下规则：

* 如果 $S$ 中的每个元素在 $T$ 中都有对应，则 $\textbf{T}$ 包含（subsumes）$\textbf{S}$。
* 类型 $\textbf{T}$ 可以被一个或更多类型包含，即 $T$ 是这些类型的交集。
* 如果 $\textbf{S}<:\textbf{T}$，那么 $\textbf{T}$ 必将是 $\textbf{S}$ 的一部分$^{*3}$。
* 如果有 $\textbf{S}<:\textbf{T}$ 且 $\textbf{S}:>\textbf{T}$，那么 $\textbf{S}$ 与 $\textbf{T}$ 等价。即Subtyping关系是反自反的。

更对于函数类型的Subtyping有：

* ${\displaystyle T_{1}≤:S_1\quad S_2≤:T_{2} \over \displaystyle S_1→S_2≤:T_1→T_2}$，即对于 $S_1→S_2$，输入 $S_1$ 是逆变的，输出 $S_2$ 是协变的$^{*4}$。

## Playground

Nothing to play with.

---

### 注解

1. 由里氏代换原则（Liskov substitution principle）：
   * 子类可以扩展超类的功能，但不能改变超类原有的功能。
   * 子类可以实现超类的抽象方法，但不能覆盖超类的非抽象方法。
   * 子类在实现超类抽象方法时，输入要更加宽松，而输出要更加严格。

2. 即对于 $S$、$T$ ，下列任何情况都是可能的：  
   * $S$ 既不是 $T$ 的子类型，也不由其派生。
   * $S$ 是 $T$ 的子类型，但不由其派生。（考虑Float与Double
   * $S$ 不是 $T$ 的子类型，但由其派生。（考虑对象上下文下的函数类型，即对于 $S$ 派生自 $T$，$S$ 的函数 $f$ 必定满足 $S→S≤:T→T$，而根据函数类型的Subtyping规则可以逆推得到 $T ≤: S$ 且 $S ≤: T$，这就意味着 $S$ 与 $T$ 等价。
   * $S$ 既是 $T$ 的子类型，又由其派生。

3. 考虑谓词函数 $P_T$ 和 $P_S$，对于域 $D$ 上的 $\textbf{S}<:\textbf{T}$ ：  
   $T=\{\ x∈D\ |\ P_T(x)\ \}$  
   $S=\{\ x∈D\ |\ P_T(x)∧P_S(x)\ \}$  
   显然，$\textbf{T}$ 是 $\textbf{S}$ 的一部分。

4. 对于函数超类型 ${T_1→T_2}$，不难理解其子类型 ${S_1→S_2}$ 的输入参数应该更加“泛化”以容纳更多的输入，而输出类型应该更加“特化”以被更多的函数接受，因为只有这样才能做到 ${T_1→T_2}$ 可以被 ${S_1→S_2}$ 安全代换。