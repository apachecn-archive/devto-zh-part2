# 什么是函数式编程

> 原文：<https://dev.to/lyfolos/what-is-functional-programming-5fj1>

首先，我们应该知道什么是*编程范例*的意思。
编程范式是对编程语言进行分类的一种方式。

让我们看看常见的编程范例包括

```
* imperative in which the programmer instructs the machine how to change its state,
  * procedural which groups instructions into procedures,
  * object-oriented which groups instructions together with the part of the state they operate on,

* declarative in which the programmer merely declares properties of the desired result, but not how to compute it
  * functional in which the desired result is declared as the value of a series of function applications, <- We will learn this
  * logic in which the desired result is declared as the answer to a question about a system of facts and rules,
  * mathematical in which the desired result is declared as the solution of an optimization problem

// https://en.wikipedia.org/wiki/Programming_paradigm 
```

当我们将在*任何*语言中运行时，你应该接受这些规则:

*   不要改变您的数据
    *   您不应该更改数据的值，或将数据更改为另一个变量。
    *   仅使用常量

这意味着你应该禁止副作用！

```
// This is not allowed!
let mut something = 20;
something += 7;

// This is allowed.
let something = 20;
let something_else = something + 7; 
```

*   每个函数都应该返回值。

```
// This is not allowed!
fn something() {
  println!("27");
}

// This is allowed.
fn something() -> i32 {
  return 5
} 
```

当你~~纯粹~~功能性的时候，你会用什么~~:~~

*   遮蔽
    *   在不同的范围内有 2 个不同的*常数*

```
let a = 20;
{
  let a = 7;
  println!("{}", a);
}
println("{}", a);

/*
Output:
7
20
*/ 
```

*   递归
    *   当~~纯粹是~~函数时，不能使用 for 或 foreach 循环。因为你只是改变了单个*变量*的值。由于不变性，这是不允许的。阶乘示例

```
fn fact(n: i32) -> i32 {
  if n == 1 {
    1
  }
  else {
    n * fact(n - 1)
  }
}

/*
fact(5);
5 * fact(4)
5 * 4 * fact(3)
5 * 4 * 3 * fact(2)
5 * 4 * 3 * 2 * fact(1) -> It's 1! Return 1 and stop recursion.
5 * 4 * 3 * 2 * 1
5 * 4 * 3 * 2
5 * 4 * 6
5 * 24
120
*/ 
```

类 Foreach 示例(python 示例:p)

```
def double_each(arr):
  if arr == []:
    return []
  return [arr[0] * 2] + double_each(arr[1:])

print(double_each([2, 7, 8]))

"""
double_each([2, 7, 8])
[4] + double_each([7, 8]))
[4] + [14] + double_each([8])
[4] + [14] + [16] + double_each([]) -> Got empty list! Return empty list and stop recursion.
[4] + [14] + [16] + []
[4] + [14] + [16]
[4] + [14, 16]
[4, 14, 16]
""" 
```

*   映射、减少和过滤
    *   Map:对列表中的每一项应用函数`f`并返回一个新的列表。
    *   Reduce:将函数`f`应用于列表中的前两个项目，继续这样操作。
    *   过滤器:如果`f`返回`true`，则添加到新列表。

地图

```
>>> list(map(lambda n: n * 2, [2, 7, 8])
[4, 14, 16] 
```

减少

```
>>> from functools import reduce
>>> reduce(lambda a, b: a + b, [2, 7, 8])
17 
```

过滤器

```
>>> list(filter(lambda n: n % 2 == 0, [2, 7, 8]))
[2, 8] 
```

# 提问

*   *什么是 lambda 演算？*
    简单来说，lambda 演算是一个描述函数及其求值的理论框架。它构成了当前几乎所有函数式编程语言的基础！
    [百科](https://en.wikipedia.org/wiki/Lambda_calculus)

*   *第一种函数式编程语言是什么？*
    口齿不清！最老的(在 FORTRAN(公式翻译)之后)，现在还在使用(例如 Clojure)。它是基于[的 s-表情](https://en.wikipedia.org/wiki/S-expression)。

*   *什么是纯粹的功能性？*
    纯粹的函数式意味着*只允许函数式编程规则*。然而，你可以在许多语言中使用纯函数。接受规则就好。

*边听边做 [MU40PROJ。KDM](https://www.youtube.com/watch?v=ppCDINkI7DQ) 由建造引擎创造者[肯·西尔弗曼](https://en.wikipedia.org/wiki/Ken_Silverman)T5】*

这是我的第一篇帖子，所以可能会犯大错误。