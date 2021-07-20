# Higher-Order Function

## 概要

高阶函数（Higher-Order Function），是一种算子。和普通函数不同，高阶函数接受少一个函数作为参数（是的！该参数是一个函数）。

## 演示

考虑 $f(x,k)=k(x)$，$g(y)=y+1$  
则函数应用 $f(2,g)$ 的结果为3，即 $f(2,g)=g(2)=2+1=3$  
那么 $f(x,k)$ 就被称为高阶函数

## Playground

给出高阶函数 $f(x,y,g)=g(x)+y$，请写出一个函数$g(x)$，使得 $f(1,2,g)=3$
