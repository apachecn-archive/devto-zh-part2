# 关于 Haskell 的注释

> 原文：<https://dev.to/yosuf/notes-on-haskell-4k51>

这些是我在大学讲课的笔记，加上一些网上和教科书上的阅读。它们还没有完成，但它们一直在我的草稿中，所以我想我应该把它们发表出来，以防其他人发现它们有用。

> 当然，除了通过冯·诺依曼瓶颈来回推动大量的单词之外，一定有一种不那么原始的方式来对商店进行大的改变。这个管道不仅是问题数据传输的文字瓶颈，而且更重要的是，它是一个智力瓶颈，使我们束缚于一次一个单词的思维，而不是鼓励我们根据手头任务的更大概念单元来思考。因此，编程基本上是计划和详述通过冯·诺依曼瓶颈的巨大的文字流量，并且这些流量中的大部分不涉及重要的数据本身，而是在哪里找到它。—约翰·巴科斯

# **内容**

*   功能性思维
*   GHCi
*   基础
*   列表
*   表达式和类型
*   功能
*   受限多态性
*   语言细节

# **思维功能**

过程化编程密切反映了底层架构，并关注于*如何*完成某事。声明式编程(包括函数式编程)关注的是*答案是什么*，并提供强大的抽象(概括)机制，同时简洁而富有表现力。

函数式编程就是用函数编程(lol)。最基本的组件是函数。它们为每个输入组合确定一个唯一的输出。

而不是问“我如何改变一个变量的值？”或者“我如何写一个循环？”，问“我想产生什么价值？”、“我怎么才能从投入中做出来？”“中间值是什么？”。

表达式只是它们的值，应该是可以互换的。

**GHCi**

GHC 是哈斯克尔的编译器。GHCi 是一个交互式环境，在这个环境中可以交互式地评估 Haskell 表达式并解释程序。它是你在命令行上用来写 Haskell 的。如果您想继续学习，您可以下载 GHCi 或者在基于浏览器的环境中使用 [repl.it](http://repl.it/languages/haskell) 。

Prelude 就是您在 GHCi 中得到的命令提示符:

```
Prelude> 
```

Enter fullscreen mode Exit fullscreen mode

**GHCi 命令**

```
:load filename … (or :l as a shortcut) — loads modules from specified 
files:reload (or :r) - repeats the last load command
:type exp (or :t) - prints the type of the expression
:quit (or :q) - exits the interpreter 
```

Enter fullscreen mode Exit fullscreen mode

# **基础知识**

基本数学运算以及使用内置函数的示例:

```
Prelude> 2+3
5

Prelude> 2+3*5
17

Prelude> (2+3)*5
25

Prelude> 2^3
8

Prelude> sqrt 2
1.4142135623730951

Prelude> sqrt 2 + 3
4.414213562373095

Prelude> sqrt (sqrt 2)
1.189207115002721

Prelude> max 4 8
8

Prelude> div 13 5
2

Prelude> mod 13 5
3

Prelude> 13 `div` 5
2

Prelude> 13 `mod` 5
3 
```

Enter fullscreen mode Exit fullscreen mode

最后两个是中缀函数的例子(注意奇怪的引号)。

# **列举**

```
Prelude> [1, 1+3, 1+3+5]
[1,4,9] 
```

Enter fullscreen mode Exit fullscreen mode

的价值..b]是一个整数列表，从 **a** 开始，到 **b** 结束。

```
Prelude> [1..6]
[1,2,3,4,5,6]

Prelude> [2..2]
[2]

Prelude> [6..1]
[]

Prelude> [6,5..1]
[6,5,4,3,2,1]

Prelude> 1:2:[]
[1,2] 
```

Enter fullscreen mode Exit fullscreen mode

一些相关功能列表:

```
Prelude> product [3,4,5]
60

Prelude> product [1..7]
5040

Prelude> product []
1

Prelude> length [1,3,5]
3

Prelude> reverse [1,3,5]
[5,3,1] 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道为什么**乘积[] = 1** ，那是因为乘法范畴中的[恒等式。一个实用的解释:](https://stackoverflow.com/questions/33732513/why-does-product-return-1) 

```
product [1,2,3]
    == product [1] * product 2:3:[]
    == product [1] * product [2] * product 3:[]
    == product [1] * product [2] * product [3] * product [] 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们用简单的递归来实现它:

```
product [] = 1
product (x:xs) = x * product xs 
```

Enter fullscreen mode Exit fullscreen mode

在 Haskell 中，字符串只是字符列表:

```
Prelude> ['H', 'e', 'l', 'l', 'o']
"Hello"

Prelude> ['a'..'z']
"abcdefghijklmnopqrstuvwxyz"

Prelude> length "Hello"
5

Prelude> reverse "Hello"
"olleH" 
```

Enter fullscreen mode Exit fullscreen mode

**【char】**与**字符串**同义。

# **表情和类型**

[![](../Images/4b5fc1ee928e8287499afe9ae781ad8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rikltKNx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2A2LJzBg86pHVL9UWRda-szQ.png) 
*哈斯克尔*中的基本类型

**类型**

Haskell 中的每个表达式都有一个类型。

不像 Java 和 Pascal 这样的语言，Haskell 有类型推理。如果我们写一个数，我们不需要告诉哈斯克尔这是一个数。它可以自己推断出这一点，所以我们不必明确地写出函数和表达式的类型来完成任务。

**类型**是值的集合，**类型类**是类型的集合。

例如，类型 *Integer* 包括像 3 和 8 这样的值，类型 class *Num* 包括类型 Integer 和 Double。

这有点类似于 Java，类是对象的集合，接口是类的集合:

[![](../Images/7c863bc38fd20722dcee1afc47bc7350.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dAloISh---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://yosuf.dev/content/images/2021/07/image.png)

查 3 的类型给了我们一个诡异的答案:

```
Prelude> :t 3
3 :: Num a => a 
```

Enter fullscreen mode Exit fullscreen mode

这里，**一个**称为类型变量。 **= >** 之前的一切称为类约束。上面说“*有几个类型* ***一个*** *一个* ***一个*** *三个类型* ***一个*** ”值 3 属于 Num typeclass 中的每个类型，包括 Integer 和 Double。Haskell 只是偷懒，不承诺特定的类型。

函数也有类型:

```
Prelude> :t head
head :: [a] -> a 
```

Enter fullscreen mode Exit fullscreen mode

这表示对于任何类型的 **a** ，函数 *head* 取一个 **a** s 的列表并返回一个 **a** 。(head 返回列表的第一个元素)。

我们甚至可以询问加号运算符的类型。我们用括号将它括起来，因为它是一个中缀运算符(意味着我们通常在值:2 + 2 之间使用它，否则 GHCi 会感到困惑，认为我们实际上在使用它):

```
Prelude> :t (+)
(+) :: Num a => a -> a -> a 
```

Enter fullscreen mode Exit fullscreen mode

上面写着:“typeclass **Num** 中有某个 type **a** ，“+”取两个 **a** 并返回另一个 **a** 。

zip:
这个功能怎么样

```
Prelude> :t zip
zip :: [a] -> [b] -> [(a, b)] 
```

Enter fullscreen mode Exit fullscreen mode

Zip 获取一个列表中的 **a** 和一个列表中的 **b** 并返回一个列表中的 **(a，b)** 对。

我们被允许指定一个函数的类型以及它的定义，这通常被认为是一个好的实践:

```
add :: Integer -> Integer -> Integer
add x y = x + y 
```

Enter fullscreen mode Exit fullscreen mode

在第一行，我们明确地告诉 Haskell，我们将要定义的 add 函数接受两个整数并返回一个整数。然后我们在第二行定义函数。

明确类型为我们和任何阅读我们代码的人提供了一些文档，但它也帮助我们找到错误。例如，假设我们有以下函数:

```
dividesEvenly x y = (y / x) * x == y 
```

Enter fullscreen mode Exit fullscreen mode

这个函数的意思是告诉我们 x 是否被 y 整除。因此，如果 x=2，y=5，我们期望(5/2)是 2，因为 2 进入 5 两次，那么 2*2 将得到 4。4==5 不为真，因此我们希望这个函数在给定 2 ^ 5 作为参数的情况下返回 False。但是，它返回 True。

```
Prelude> dividesEvenly 2 5
True 
```

Enter fullscreen mode Exit fullscreen mode

这是因为，在 Haskell 中，5/2 不是 2，而是 2.5。解决这个问题的一个方法是指定我们期望 x 和 y 是整数:

```
Prelude> dividesEvenly :: Int -> Int -> Bool
Prelude> dividesEvenly x y = (y / x) * x == y 
```

Enter fullscreen mode Exit fullscreen mode

然而，这给了我们一个错误:

```
<interactive>:3:1: error:    
    • No instance for (Fractional Int)
            arising from a use of ‘dividesEvenly’ 
```

Enter fullscreen mode Exit fullscreen mode

它告诉我们 Int 不是 typeclass Fractional 的成员。 **/** 运算符只对分数有效。原来我们要的运算符是 **`div`** :

```
Prelude> dividesEvenly :: Int -> Int -> Bool
Prelude> dividesEvenly x y = (y `div` x) * x == y
Prelude> divideEvenly 2 5 False 
```

Enter fullscreen mode Exit fullscreen mode

通过指定函数的类型，我们能够找到一个我们可能忽略的 bug。

# 功能

**中缀运算符**

Haskell 允许通常的中缀符号:

```
Prelude> 1 + 2*3
11 
```

Enter fullscreen mode Exit fullscreen mode

中缀运算符`+`和`*`指的是带有两个参数的函数。运算符也有优先级和关联。为了单独引用这些类型的函数，我们将它们放在括号中，写成`(+)`和`(*)`。所以我们可以这样使用它们:

```
Prelude> (*) 2 5
10
Prelude> (+) 1 ((*) 2 5)
11 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有引用二元函数的普通标识符:

```
div :: Int -> Int -> Int
mod :: Int -> Int -> Int 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用反引号将它们作为中缀函数:

```
Prelude> (1987 `div` 100) `mod` 4 
```

Enter fullscreen mode Exit fullscreen mode

与
相同

```
Prelude> mod (div 1987 100) 4 
```

Enter fullscreen mode Exit fullscreen mode

**Int 上的函数**

```
(+) :: Int -> Int -> Int
(-) :: Int -> Int -> Int
(*) :: Int -> Int -> Int
(^) :: Int -> Int -> Int
div :: Int -> Int -> Int
mod :: Int -> Int -> Int
abs :: Int -> Int
negate :: Int -> Int 
```

Enter fullscreen mode Exit fullscreen mode

例子:

```
Prelude> abs 3
3

Prelude> negate 3
-3

Prelude> abs (negate 3)
3

Prelude> negate (negate 3)
3

Prelude> abs 0
0

Prelude> negate 0
0 
```

Enter fullscreen mode Exit fullscreen mode

**关系运算符**

```
(<) :: Int -> Int -> Bool
(<=) :: Int -> Int -> Bool
(>) :: Int -> Int -> Bool
(>=) :: Int -> Int -> Bool
(==) :: Int -> Int -> Bool
(/=) :: Int -> Int -> Bool 
```

Enter fullscreen mode Exit fullscreen mode

例子:

```
Prelude> 2 < 3
True

Prelude> 2 < 3
True

Prelude> 2 < 2
False
Prelude> 2 /= 2
False

Prelude> 2 /= 3
True 
```

Enter fullscreen mode Exit fullscreen mode

**过载**

`(==)`函数有几种类型:

```
(==) :: Int -> Int -> Bool
(==) :: Bool -> Bool -> Bool
(==) :: Char -> Char -> Bool
(==) :: Float -> Float -> Bool
(==) :: Eq a => a -> a -> Bool
(<=) :: Ord a => a -> a -> Bool 
```

Enter fullscreen mode Exit fullscreen mode

**建筑布尔表达式**

```
(&&) :: Bool -> Bool -> Bool
(||) :: Bool -> Bool -> Bool
not :: Bool -> Bool 
```

Enter fullscreen mode Exit fullscreen mode

例子:

```
Prelude> not True
False

Prelude> False || True
True

Prelude> 1 < 2 || 1 > 2
True 
```

Enter fullscreen mode Exit fullscreen mode

使用关系运算符的函数:

```
small :: Int -> Bool
small n = 0 <= n && n < 10 
```

Enter fullscreen mode Exit fullscreen mode

**类型间函数**

Haskell 没有类型之间的隐式转换。您必须使用显式函数:

[![](../Images/c348bebf2d3ecda56279a954a32d6dd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cwu66rXI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://yosuf.dev/content/images/2021/07/image-1.png)

要使用`ord`和`chr`功能，必须导入`Data.Char`库模块:

```
Prelude> floor 3.7
3

Prelude> ceiling 3.7
4

Prelude> :m + Data.Char

Prelude> ord 'a'
97

Prelude> chr 98
'b' 
```

Enter fullscreen mode Exit fullscreen mode

**Char**的功能

```
isDigit :: Char -> Bool
isDigit c = ’0’ <= c && c <= ’9’
isUpper, isLower :: Char -> Bool
isUpper c = ’A’ <= c && c <= ’Z’
isLower c = ’a’ <= c && c <= ’z’
isAlpha :: Char -> Bool
isAlpha c = isUpper c || isLower c
ord :: Char -> Int
chr :: Int -> Char 
```

Enter fullscreen mode Exit fullscreen mode

例子:

```
Prelude> :m + Data.Char

Prelude> isDigit '2'
True

Prelude> isLower 'B'
False

Prelude> ord '1'
49

Prelude> chr 65
'A'

Prelude> chr 48
'0'

Prelude> chr 32
' '

Prelude> chr (ord 'b')
'b'

Prelude> chr (ord 'b' + 3)
'e' 
```

Enter fullscreen mode Exit fullscreen mode

**浮动功能**

```
(+) :: Float -> Float -> Float
(-) :: Float -> Float -> Float
(*) :: Float -> Float -> Float
(/) :: Float -> Float -> Float
(^) :: Float -> Int -> Float
abs :: Float -> Float
negate :: Float -> Float
sin :: Float -> Float
asin :: Float -> Float
exp :: Float -> Float
log :: Float -> Float
sqrt :: Float -> Float 
```

Enter fullscreen mode Exit fullscreen mode

**守护定义**

```
maxTwo :: Int -> Int -> Int
maxTwo x y
    | x >= y = x
    | otherwise = y 
```

Enter fullscreen mode Exit fullscreen mode

布尔表达式`x >= y`称为保护。守卫是按顺序测试的，所以如果我们有:

```
maxThree :: Int -> Int -> Int -> Int
maxThree x y z
    | x >= y && x >= z = x
    | y >= x && y >= z = y
    | otherwise = z 
```

Enter fullscreen mode Exit fullscreen mode

可以简化为:

```
maxThree :: Int -> Int -> Int -> Int
maxThree x y z
    | x >= y && x >= z = x
    | y >= z = y
    | otherwise = z 
```

Enter fullscreen mode Exit fullscreen mode

**条件表达式**

Haskell 还有一个表达形式:`if boolexp then exp1 esle exp2` *。*

所以与其:

```
maxTwo :: Int -> Int -> Int
maxTwo x y
    | x >= y = x
    | otherwise = y 
```

Enter fullscreen mode Exit fullscreen mode

我们可以写:

```
max :: Int -> Int -> Int
max x y = if x >= y then x else y 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用现有函数来定义函数。所以你可以把这个:

```
maxThree :: Int -> Int -> Int -> Int
maxThree x y z
    | x >= y && x >= z = x
    | y >= z = y
    | otherwise = z 
```

Enter fullscreen mode Exit fullscreen mode

成这样:

```
maxThree x y z = maxTwo x (maxTwo y z) 
```

Enter fullscreen mode Exit fullscreen mode

**本地定义**

您可以有进一步缩进的局部定义，并且必须对齐:

```
sumSquares :: Int -> Int -> Int
sumSquares n m
    = sqN + sqM
        where
        sqN = n*n
        sqM = m*m 
```

Enter fullscreen mode Exit fullscreen mode

您可以在所有防护中使用本地定义，以及右边的表达式:

```
maxsq :: Int -> Int -> Int
maxsq x y
    | sqx > sqy = sqx
    | otherwise = sqy
    where
    sqx = sq x
    sqy = sq y

    sq :: Int -> Int
    sq z = z*z 
```

Enter fullscreen mode Exit fullscreen mode

# **受约束的多态性**

例如，“元素”函数接受两个参数，并询问“第一个参数是否出现在第二个参数中(第二个参数是某种结构)？并且适用于多种类型:

```
Prelude> elem ‘e’ “Hello”
True

Prelude> elem 3 [1..5]
True 
```

Enter fullscreen mode Exit fullscreen mode

这个函数几乎是多态的:被比较的事物的类型必须支持*相等测试*，这反映在 elem 的约束类型:

```
Prelude> :t elem
elem :: (Eq a, Foldable t) => a -> t a -> Bool 
```

Enter fullscreen mode Exit fullscreen mode

这里， **Eq a** 是对 **a** 类型的约束。类型 **a** 必须属于 typeclass **Eq** :支持诸如 Char、Int 和 Integer 等等式测试的类型。

# **语言细节**

**缩进**

在文件中，Haskell 使用缩进来决定另一个定义从哪里开始，所以所有的定义必须有相同的缩进。以下将被视为两种定义并且是合法的:

```
square
    :: Integer -> Integer
square n = n * n 
```

Enter fullscreen mode Exit fullscreen mode

以下是不合法的，Haskell 认为第一个是一个定义，第二个是两个定义:

```
square :: Integer -> Integer
    square n = n * n

square
:: Integer -> Integer 
```

Enter fullscreen mode Exit fullscreen mode

* * *

> 订阅我的[子栈](https://yosuf.substack.com/subscribe)以获得未来帖子的通知。