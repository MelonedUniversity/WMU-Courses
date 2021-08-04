# System F

系统F

## 概要

系统F是一种多态化的 $λ$ 演算，它形式化了编程语言中参数多态的概念。它在 $λ^{→}$ 类型系统的基础上引入了类型参数的概念，允许由类型构造值（即`Type → Term`）。系统F又被称为二阶 $λ$ 演算$^{*1}$。

## 参数多态

说的明白点就是泛型（或C++中的模板），比如：

```Java
ArrayList<Integer> arrInteger;
ArrayList<Double> arrDouble;
ArrayList<Float> arrFloat;
```

得益于参数多态，我们可以通过`ArrayList<E>`构造出任意类型的列表，因为我们引入了一个以前从没有过的类型参数`E`从而极大的提升了多态性。系统F可以看作是这种构造方式的理论依据。

## 形式化表述

相较于 $λ^{→}$，系统F在每个 $λ$ 项前引入了一个类型参数来实现参数多态，比如这个恒等函数：

$Λα.λx^α.\ x:∀α.α→α$

在 $λ^{→}$ 中我们是这样定义恒等函数的：

$λx:α.\ x:α→α$

但注意到 $α$ 是一个**具体的类型**（我在 $λ^{→}$ 那章的末尾提到过），要想覆盖所有类型的恒等函数，我们就要这样做：

$λx:α.\ x$  
$λx:β.\ x$  
$λx:γ.\ x$  
$...$

像极了我们在C语言中的行为：

```C
int id_int(int a){ return a; }
float id_float(float a){ return a; }
double id_double(double a){ return a; }
...
```

但有了系统F，我们的代码就升级成了这样：

```C++
template<typename T>
T id(T a) {
    return a;
}
```

---

### 注解

1. 之所以称之为二阶 $λ$ 演算（$λ_2$），是因为在柯里-霍华德同构下系统F对应于二阶逻辑$^{*2}$。在 $λ$ 立方体（Lambda Cube）中，自左下角至右上角表达能力逐渐增强，$λ2$ 居于 $λ^{→}$ 的上方。

    <img style="background:white;" src="resources/lambda_cube.svg" width="300px">

2. 相较于一阶逻辑$^{*3}$，二阶逻辑允许量化谓词。如 $∀a,b(a=b→∀P(P(a)↔P(b)))$：对于每个 $a$ 和 $b$，如果 $a$ 等于 $b$，那么对于任意性质 $P$，$P(a)$ 当且仅当 $P(b)$。
3. 又称谓词逻辑，如 $∀a({\text{Phil}}(a)→{\text{Schol}}(a))$：对于每个 $a$，若 $a$ 为哲学家，则 $a$ 为学者。
