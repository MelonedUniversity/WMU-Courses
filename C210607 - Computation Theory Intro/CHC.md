# Curry-Howard Correspondence

柯里-霍华德对应

## 概要

**柯里-霍华德对应**，又称柯里-霍华德同构（Curry-Howard Isomorphism），是数学证明和计算机程序间的紧密联系：对命题的描述可以看作是计算机程序中的类型，而对命题的证明可以看作是计算机程序的语言构造。这种高度相似表明了形式逻辑系统和公式计算（computational calculus）在一定层次上是同构的，于是我们可以用程序来进行数学证明。

## 命题即类型，证明即程序

对于相继式 $a_1:A_1,a_2:A_2,a_3:A_3,...,a_n:A_n\vdash E:B$，其前件可以看作是具有一系列类型的一系列值，而后件则可看作是具体的程序构造。由命题 $a_1:A_1,a_2:A_2,a_3:A_3,...,a_n:A_n$ 证明 $E:B$ 的过程同构于给定一系列具有特定类型的值而编写计算机程序的过程，即当 $a_1,a_2,a_3,...,a_n$ 分别具有类型 $A_1,A_2,A_3,...,A_n$ 时，程序 $E$ 的运行结果将具有类型 $B$。

## 与形式化证明的关联

柯里霍华德同构给出了证明和程序间的密切联系，证明和程序的等价性使得数学研究工作者们可以使用辅助证明器（ Proof Assistant）来解决一些人工难以证明的数学问题。常见的面向证明（Proof Orinted）的语言有Agda、Coq、F*、Idris、Lean等。

比如，我们现在使用Coq证明任何一个数乘二都等于这个数与自身相加：

```Coq
Fixpoint plus (a b : nat) :=
match a with
| O => b
| S a' => S (plus a' b)
end.

Fixpoint times (a b : nat) :=
match b with
| O => 0
| S b' => plus (times a b') a
end.

Theorem t : forall a,
  times a 2 = plus a a.
  intros.
  reflexivity.
Qed.
```

Coq中的自然数采用皮亚诺公理构造，即一个数要么是0要么是0的后继。首先我们据此构造了加法和乘法，然后我们提出一个命题：`times a 2 = plus a a`，对上下文进行变量引入并化简，最终完成了证明。
