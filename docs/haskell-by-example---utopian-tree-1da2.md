# 哈斯克尔的例子-乌托邦之树

> 原文：<https://dev.to/jvanbruegge/haskell-by-example---utopian-tree-1da2>

在这个系列中，我们以一种适当的、功能性的方式解决了来自 Haskell 中 Hackerrank 的编码挑战。在上一部分中，我没有提到如何在你自己的 PC 上运行 Haskell，所以我想解决这个问题。最简单的方法就是下载[栈](https://docs.haskellstack.org/en/stable/README/)。然后在某个地方创建一个以`.hs`结尾的文件，在同一个目录下打开一个终端。键入`stack ghci`打开一个交互式的 Haskell REPL，第一次会花一些时间，因为 stack 必须先下载 Haskell 编译器。一旦完成，你可以输入`:load <filename>.hs`来编译和检查你的代码。也可以用`:type <expr>`得到一个表达式的类型。

# 问题

乌托邦树每年有两次生长高峰，一次在春天，一次在夏天。春天，这棵树长高了一倍，夏天，它长高了一米。你种了一棵树，最初有一米高。生长突增后的树有多大？

为了`n == 5` :
，我们来回顾一下正在发生的事情

```
Period  Height
  0       1      We start with a 1 meter tree
  1       2      Double the size
  2       3      Increase by one meter
  3       6      Double again
  4       7      Plus one meter
  5       14     And a final double 
```

# 输入格式

我们将收到多条线路，每条线路有一个号码。第一行是后面的行数。另一个数字是增长的数量。目标是回答每个数字之后树有多大。

例如(参考前面的表格)当我们得到

```
3
4
2
0 
```

我们必须返回(记住第一行只是长度)

```
7
3
1 
```

如果你不想破坏解决方案，现在就去 [Hackerrank](https://www.hackerrank.com/challenges/utopian-tree/problem) 自己先解决问题。

只是说明一下，Hackerrank 的 Haskell 模板很恐怖，不要用。稍后您将看到完整的解决方案比单独的模板要短得多。

# 解

如果我们稍微思考一下这个问题，它可以归结为做一件事:将当前高度增加一倍或两倍(这里,`n`是给我们的数字)。在命令式编程语言中，你可以使用 for 循环来完成这个任务，但是在 Haskell 中没有 for 循环。原因很简单，你不允许改变变量，所以你不能改变循环中的`i`变量。但是这一点也没有限制，相反，它有一些有趣的结果:在 Haskell 中，将 double 列为控制结构。那是什么意思？您在本系列的最后一部分已经看到了一些:

> 现在*对于每一行*(也就是列表中的元素)，我们希望首先将字符串拆分成单词，然后*对于每个单词*，我们希望将字符串转换成一个整数。

在其他语言中,“for each x”是使用 for 循环的明确信号，但是这里我们使用了`map`和列表。

对于我们的新问题来说,`map`是不够的，因为我们必须记住树的最后高度。但幸运的是，还有一个函数可以做到这一点，`foldl`(对于那些懂 JavaScript 的人来说，`array.reduce`基本上是同一个函数)。

```
foldl :: (b -> a -> b) -> b -> [a] -> b 
```

`foldl`从记忆值和当前值到新记忆值的函数作为第一个参数，初始值作为第二个参数，值列表作为第三个参数。它在最后返回记忆的值。

那么，让我们从我们的 solve 函数开始，注意这个函数将只计算一棵树的高度，而不是所有的树。

```
solve :: Int -> Int
solve n = foldl fn init list 
```

好了，现在我们要找到`fn`、`init`和`list`的值。初始值应该是清楚的，问题已经说明了我们从一棵高度为 1 的树开始。

```
solve :: Int -> Int
solve n = foldl fn 1 list 
```

`list`有点复杂。还记得我说过把 double 列为控制结构(即 for 循环)吗？我们想增加一倍，并交替增加高度。我们可以定义一个列表来准确表达这种行为:

```
solve :: Int -> Int
solve n = foldl fn 1 {- stuff missing -} [(*2), (+1)] 
```

现在我们有两个函数的列表，都是从`Int`到`Int`。但是我们不想这样做两次，而是更多次！哈斯克尔的另一个优点来了:懒惰。懒惰意味着价值只在需要的时候才被计算。比如:

```
first :: [Int] -> Int
first [a, b] = a

x = first [1, error "this will crash"] 
```

这个代码片段会工作得很好，即使我们在那里有一个运行时错误。这样做的原因是，我们从来没有尝试使用错误的值，所以它从来没有得到评估。但是，这也有一个好的副作用:Haskell 可以处理无限列表！当然，一个无限的列表永远不会适合内存，但是由于 Haskell 只评估它需要的东西，列表永远不会被完全分配。

```
allEvenNumbers = filter even [1 ..]
x = take 6 allEvenNumbers 
```

这里我们过滤所有正数的列表，只返回偶数。在其他编程语言中，这个代码片段会导致堆栈溢出错误或无限循环。

回到我们的`solve`函数，我们可以使用`cycle`通过永远重复给定的列表来创建一个无限列表。

```
solve :: Int -> Int
solve n = foldl fn 1 {- stuff missing -} cycle [(*2), (+1)] 
```

但是我们不想要无限增长爆发之后的结果，而是在`n`之后！从上一部分我们已经知道如何做到这一点:

```
solve :: Int -> Int
solve n = foldl fn 1 . take n . cycle $ [(*2), (+1)] 
```

我们简单地从我们的无限列表中选择`take` `n`值。现在最后一个问题是，`fn`该怎么做？在这里，我们可以使用 REPL 来帮助我们。如果我们用占位符而不是`fn`输入我们当前的解决方案，Haskell 会告诉我们它所期望的:

```
Prelude> foldl _ 1 $ cycle [(*2), (+1)]

<interactive>:7:7: error:
    • Found hole: _ :: b -> (Integer -> Integer) -> b
      Where: ‘b’ is a rigid type variable bound by
               the inferred type of it :: Num b => b at <interactive>:7:1-30
    • In the first argument of ‘foldl’, namely ‘_’
      In the expression: foldl _ 1
      In the expression: foldl _ 1 $ cycle [(* 2), (+ 1)]
    • Relevant bindings include it :: b (bound at <interactive>:7:1) 
```

这条信息可能看起来很吓人，但让我们一步一步地消化它。第一行已经告诉了我们想要知道的大部分内容:占位符的类型是`b -> (Integer -> Integer) -> b`。第三行告诉我们`b`必须是一个数字类型(这就是`Num b => b`的意思)。原因是我们代码中的`1`仍然可以是任何数字类型。我们可以添加一个类型签名，强制它成为一个整数:

```
Prelude> foldl _ (1 :: Integer) $ cycle [(*2), (+1)]

<interactive>:10:7: error:
    • Found hole: _ :: Integer -> (Integer -> Integer) -> Integer
    • In the first argument of ‘foldl’, namely ‘_’
      In the expression: foldl _ (1 :: Integer)
      In the expression: foldl _ (1 :: Integer) $ cycle [(* 2), (+ 1)]
    • Relevant bindings include
        it :: Integer (bound at <interactive>:10:1) 
```

正如您现在看到的，我们需要一个函数，它接受一个值和一个函数，并返回应用于该值的函数的结果。这里我们可以使用另一个伟大的工具:Hoogle。如果我们前往[haskell.org/hoogle](https://www.haskell.org/hoogle/?hoogle=Integer+-%3E+%28Integer+-%3E+Integer%29+-%3E+Integer)并输入我们从 REPL 收到的类型签名，我们会得到一堆结果。如果我们忽略更复杂的前几个条目，我们很快就会看到列表中一个我们已经知道的条目:
[![$](img/99ae24e3856cf054a8558c3f166a09b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vyDENtzg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mAr2c2z.png)

`$`操作员。唯一的问题是论点的顺序不对。但是这不是问题，因为有一个`flip`函数交换参数。所以现在我们可以完成我们的求解函数:

```
solve :: Int -> Int
solve n = foldl (flip ($)) 1 . take n . cycle $ [(*2), (+1)] 
```

我们有两个括号把操作符括起来，把它作为普通函数而不是操作符使用。

# 使其可执行

同样，solve 函数是不够的，我们需要从标准输入读取并写入标准输出。我们将再次使用`interact`函数。

```
solve :: Int -> Int
solve n = foldl (flip ($)) 1 . take n . cycle $ [(*2), (+1)]

main :: IO ()
main = interact $ -- stuff missing 
```

好的，我们需要除了第一行以外的所有行。我们上次已经做过了:

```
solve :: Int -> Int
solve n = foldl (flip ($)) 1 . take n . cycle $ [(*2), (+1)]

main :: IO ()
main = interact $ tail . lines 
```

现在，*对于每一行*我们要将字符串转换成数字，运行 solve 函数，并将数字转换回字符串

```
solve :: Int -> Int
solve n = foldl (flip ($)) 1 . take n . cycle $ [(*2), (+1)]

main :: IO ()
main = interact $ map (show . solve . read) . tail . lines 
```

最后，我们希望在列表中的所有字符串之间放置一个换行符。已经有一个名为`unlines`的函数，因为它是`lines`的反函数。

```
solve :: Int -> Int
solve n = foldl (flip ($)) 1 . take n . cycle $ [(*2), (+1)]

main :: IO ()
main = interact $ unlines . map (show . solve . read) . tail . lines 
```

就是这样！两行代码就有了一个完整的解决方案，而不需要像 code golf 这样的东西。甚至比上次更多的是，我们自己并没有写很多，我们只是从标准库中组合函数来形成更大的函数。这是函数式编程的核心！

# 性能 PSA

虽然我们当前的解决方案可以工作，并且可以通过 Hackerrank 的测试，但是它不适合生产。原因是(不想要的)懒惰。`foldl`的定义是这样的:

```
foldl _ x [] = x
foldl f x (y:xs) = foldl f (f x y) xs 
```

问题是 Haskell 在这一点上并没有对`(f x y)`求值，而是仅仅分配了一个所谓的“thunk”来告诉*T2 如何计算它。当我们浏览列表时，我们可以看到问题:* 

```
x = foldl (+) 0 [1,2,3,4]
x = foldl (+) (0 + 1) [2,3,4]
x = foldl (+) ((0 + 1) + 2) [3, 4]
x = foldl (+) (((0 + 1) + 2) + 3) [4]
x = foldl (+) ((((0 + 1) + 2) + 3) + 4) []
x = ((((0 + 1) + 2) + 3) + 4) 
```

在那个时间点，圆括号中的表达式都不被计算，但是对于每个`+`必须分配一个 thunk。这是对空间的巨大浪费和性能的严重下降。幸运的是在标准库中有一个*严格*(如《不懒》)版本的`foldl`:`foldl'`。我们也可以用`BangPatterns`扩展自己实现它(我将在以后的文章中更深入地讨论语言扩展)。

```
foldl' _ !x [] = x
foldl' f !x (y:xs) = foldl f (f x y) xs 
```

这个感叹号导致参数是严格的，即它将在函数的其余部分之前被求值。有了它，我们的评价看起来是这样的:

```
x = foldl (+) 0 [1,2,3,4]
x = foldl (+) (0 + 1) [2,3,4]
x = foldl (+) (1 + 2) [3, 4]
x = foldl (+) (3 + 3) [4]
x = foldl (+) (6 + 4) []
x = 10 
```

这一次，我们没有分配一堆 thunks，我们的函数运行得很快。我们不必自己定义这个函数，我们可以简单地从标准库中导入它:

```
module Main where

import Data.Foldable (foldl')

solve :: Int -> Int
solve n = foldl' (flip ($)) 1 . take n . cycle $ [(*2), (+1)]

main :: IO ()
main = interact $ unlines . map (show . solve . read) . tail . lines 
```

一般情况下，千万不要用`foldl`总是用`foldl'`。

# 关闭思绪

我希望这个例子足够简单，可以让你更熟悉这门语言。如果您有任何问题，无论是关于我的代码还是 Haskell，请随时提问。