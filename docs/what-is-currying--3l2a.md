# 什么是 Currying？

> 原文：<https://dev.to/__namc/what-is-currying--3l2a>

原文发表于:[https://namc.in/2018-02-22-currying](https://namc.in/2018-02-22-currying)

## 灵感

这篇文章是在我看到一条推特后写的，上面写道

> 每个函数式编程教程:
> 
> 好了，我们来谈谈 currying。Currying 就是当你分解一个函数 tha-
> *向下滚动*
> const Y = f =>(g =>g(g))(g =>f(x =>g(g)(x))

并意识到我们需要一个更简单的教程来理解 currying，它背后的逻辑，细微差别和用法。

在本教程中，我们将使用 Haskell，但这可以扩展到任何函数式编程语言。

## 高阶函数！

在数学中，高阶函数被称为泛函。但是为了简化它，对于一般的软件工程师来说，HOF 仅仅是一个函数

*   要么接受一个函数作为参数
*   或者返回一个函数作为结果

一个众所周知的高阶函数是 map，它对集合中的每个元素执行一个动作，例如:

```
-- Haskell
map (\x -> 2 * x) [1, 2, 3, 4, 5] -- => [2, 4, 6, 8, 10] 
```

Enter fullscreen mode Exit fullscreen mode

在通常的命令式编程中，函数可以接受值，比如整数和字符串(一阶函数)，并返回其他类型的值。

考虑一个函数，`f`使得-

```
f a b c = a + b - c 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想推广上述函数中的操作符呢？这将使我们的运营商也成为一个变量。对吗？让我们看看如何定义一个更一般化的`f`版本，其中`g`和`h`代表操作函数。

```
let f g h a b c = a `g` b `h` c

> f (+) (-) 2 3 4 -- returns 1
> f (*) (/) 2 3 4 -- returns 1.5 
```

Enter fullscreen mode Exit fullscreen mode

那很简单！但是，我们提到 HOFs 也可以返回一个函数。是的，我们可以创建一个函数，它接受一个函数和一个参数并返回另一个函数，后者接受一个参数并返回一个结果。例如:

```
let g f n = (\m -> m `f` n) 

> f = g (+) 2  
> f 10 -- returns 12

> f = g (*) 2
> f 10 -- returns 20 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`(\m -> m` f `n)`构造是一个 1 参数`m`的匿名函数，它将`f`应用于`m`和`n`。使用这个匿名函数，我们可以定义`g h 2`，它是一个接受一个参数`g`并对其操作`h`的函数。

根据维基百科，Scheme 是第一种引入适当的高阶函数作为一等公民的语言，然而第一次提到 Scheme 可以追溯到弗雷格在《function und Begriff》(1891)。

## 逢迎

Currying 是一种将多个参数的函数转换为单个参数的函数的技术，该函数返回一个参数的函数，该函数返回一个参数的函数...直到它返回一个值。

curried 函数是返回函数结果的函数。完全规范的函数是一个单参数函数，它或者返回一个普通结果，或者返回一个完全规范的函数。

注意，一个 curried 函数必然是一个高阶函数，因为它返回一个函数作为结果。

举个例子，我们有一个两个参数的函数，比如(+)。但是，如果你可以定义同一个函数，只给它一个参数，从而返回一个函数，你可以用它来添加第一个参数，现在在这个新函数中，到其他的东西。

```
f n = (\m -> n + m)

> g = f 10
> g 8 -- would return 18
> g 4 -- would return 14 
```

Enter fullscreen mode Exit fullscreen mode

同样可以用 javascript 写成像:

```
function add (a) {
  return function (b) {
    return a + b;
  }
}

add(3)(4) 
```

Enter fullscreen mode Exit fullscreen mode

怎么能让它更抽象呢？让我们定义`curry`，它接受一个函数和一个参数，这样

```
curry f n = \m -> f n m

g = curry (+) 5
> g 10 -- returns 15

h = curry (*) 5
> g 10 -- returns 50 
```

Enter fullscreen mode Exit fullscreen mode

如果我们检查上面定义的`curry`函数的类型`:t`，我们会发现，

```
curry :: (a -> b -> c) -> a -> (b -> c) 
```

Enter fullscreen mode Exit fullscreen mode

## 哈斯克尔函数

默认情况下，Haskell 处理所有函数。Haskell 中没有多参数函数。你有的只是一个参数的函数，其中一些可能返回一个参数的新函数。所以你可以像在其他语言中一样定义一个多参数函数，你会自动得到它的一个简化版本，而不需要你自己写 lamdas。

例如，以连接两个字符串的函数`(++)`为例。

```
:t (++)
(++) :: [a] -> [a] -> [a] 
```

Enter fullscreen mode Exit fullscreen mode

类型定义可以理解为，`(++)`接受一个列表，返回一个`[a] -> [a]`类型的函数。结果函数可以接受另一个列表，我们得到一个新的类型为`[a]`的列表。

如果你熟悉其他语言，你可以把`f a b c`关联成 Lisp 的`(((f a) b) c)`或者 Java 的`f(a, b, c)`。这是有意义的，因为在 Haskell 中，函数`f`是默认设置的。

然而，当你在分析类型时，关联是从右到左的，所以`[a] -> [a] -> [a]`相当于`[a] -> ([a] -> [a])`，这意味着当你对函数应用一个参数时，你会得到一个`[a] -> [a]`类型的函数。

类似地，您可以分析`map`，它接受一个函数`(a -> b)`和一个列表`[a]`，并返回一个转换后的列表`[b]`，这样函数就映射到了`[a]`的所有元素上。

```
:t map
map :: (a -> b) -> [a] -> [b] 
```

Enter fullscreen mode Exit fullscreen mode

在 ghci 中尝试以下片段

```
(*)
(*) 5
(*) 5 10
map
map (\x -> head x)
map (\x -> head x) ["hello", "haskell", "kitty"]
map head
map head ["hello", "haskell", "kitty"] 
```

Enter fullscreen mode Exit fullscreen mode

## 局部应用

部分应用是指当你调用一个带有 1 个或多个参数的函数来取回一个仍然接受参数的函数。这允许我们编写更短更简洁的代码。

部分应用程序可以作为匿名函数的替代品。让我们看一些例子。

```
(\x -> take 2 x)

-- can be written as
(take 2) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想用一个运算符，说`(+)`而不是`take`呢？对于中缀函数，您可以使用[部分](https://wiki.haskell.org/Section_of_an_infix_operator)来部分应用它。

```
(\x -> 2 + x)
-- can be written as 
(2+)

-- and 
(\x -> x + 2)
-- can be written as
(+2) 
```

Enter fullscreen mode Exit fullscreen mode

代替`(+)`，您可以使用任何二元函数来定义部分。

```
(\xs -> 2 elem' xs)

-- can be written as
(2elem') 
```

Enter fullscreen mode Exit fullscreen mode

这里要注意的一点是，由于每个函数在默认情况下都是 curried 化的，并且只接受一个参数，所以节可以与任何函数一起使用。例如，

```
let f a b c d = a + b + c

-- such that f is (+)

> (2f) 3 4 5 -- returns 14 
```

Enter fullscreen mode Exit fullscreen mode

## 穿针引线和图案匹配

我们理所当然地认为我们可以拥有嵌套模式和多个术语的模式，但实际上对于编译器来说，唯一能做的就是在单个值的顶级构造函数上进行分支。所以编译器的第一步是将嵌套模式(以及超过一个值的模式)转化为更简单的模式。

举个例子，一个简单的算法可能会把你的函数转换成这样:

```
myFunc = \x y -> case x of
    0 -> case y of
        0 -> 0
        _ -> x `some_operation` y
    1 -> case y of
        1 -> 1
        _ -> x `some_operation` y
    _ -> x `some_operation` y 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，在上面的实现中有很多警告。

*   这个术语被重复了很多次
*   该函数在开始处理一个案例之前需要两个参数

参考[一个受有限自动机理论启发的术语模式匹配编译器](https://www.classes.cs.uchicago.edu/archive/2011/spring/22620-1/papers/pettersson92.pdf)，进一步讨论我们如何改进它。

无论如何，在下面的表格中，它实际上应该更清楚一点 currying 步骤是如何发生的。我们可以直接替换这个表达式中的`x`来看看`myFunc 0`做什么:

```
myFunc 0 = \y -> case 0 of
    0 -> case y of
        0 -> 0
        _ -> 0 `some_operation` y
    1 -> case y of
        1 -> 1
        _ -> 0 `some_operation` y
    _ -> 0 `some_operation` y 
```

Enter fullscreen mode Exit fullscreen mode

现在这仍然是一个λ，所以没有进一步的减少。

如果我们想让 GHC 在只提供一个参数后做更多的计算，我们需要改变函数的定义。这里有一个时间/空间的权衡。所以 GHC 让程序员来做这个选择。例如，您可以显式地编写

```
myFunc 0 = \y -> case y of
    0 -> 0
    _ -> 0 `some_operation` y

myFunc 1 = \y -> case y of
    1 -> 1
    _ -> 1 `some_operation` y

myFunc x = \y -> x `some_operation` y 
```

Enter fullscreen mode Exit fullscreen mode

然后`myFunc 0`会简化成一个更小的表达式。

## 作文

组合和应用操作符对于编写简洁灵活的代码来说非常方便。

*   **复合运算符** `(.)`将函数链在一起。
*   **应用运算符** `($)`将左边的函数应用于右边的自变量

`f $ x`相当于`f x`。然而`($)`在所有运算符中优先级最低，所以我们可以用它来去掉括号:`f (g x y)`相当于`f $ g x y`。

当我们需要对同一个参数应用多个函数时，这也很有帮助:

```
map ($2) [(2+), (10-), (20/)] 

-- would yield 
[4,8,10] 
```

Enter fullscreen mode Exit fullscreen mode

以下表达式都是等价的

```
f (g (h (x + y + z)))   -- 1

(f . g . h) (x + y + z) -- 2

f $ g $ h $ x + y + z   -- 3

f . g . h $ x + y + z   -- 4 
```

Enter fullscreen mode Exit fullscreen mode

`(.)`和`($)`是不同的东西。更多可以在这里阅读- [学你一个哈斯克尔($)](http://learnyouahaskell.com/higher-order-functions#function-application)