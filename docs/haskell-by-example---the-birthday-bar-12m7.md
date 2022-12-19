# 哈斯克尔的例子-生日酒吧

> 原文：<https://dev.to/jvanbruegge/haskell-by-example---the-birthday-bar-12m7>

这是我向您展示如何以适当的、功能性的方式解决 Haskell 中的编码挑战的系列文章的开始。如果你不认识 Haskell，没关系，我会详细解释一切。这些问题来自 Hackerrank，所以您可以自己尝试一下，并尝试一下实现。

## 问题

你有一块巧克力，每个方块上都有一个数字。你想和你的朋友分享这个横条，这样横条的长度就等于他的出生月份，这些方格中的数字之和就是出生日期。你必须打印出一个给定的巧克力块能做成多少这样的切片。

举个例子，你有巧克力棒。你朋友的生日在 4 点。二月的。在这种情况下，有两个可能的切片:`[2, 2]`和`[1, 3]`，因为它们的长度为 2(月)，总和为 4(日)。

## 输入格式

我们将通过标准输入接收细节，其中第一个 like 只包含巧克力棒的长度，第二个 like 包含巧克力棒——由空格分隔——最后一行首先是生日，然后是出生月份，也由空格分隔。我们之前的例子应该是这样的:

```
5
2 2 1 3 2
4 2 
```

我们知道，我们收到的所有值都将大于零。

如果你不想破坏解决方案，现在就去 [Hackerrank](https://www.hackerrank.com/challenges/the-birthday-bar/problem) 自己先解决问题。

只是说明一下，Hackerrank 的 Haskell 模板很恐怖，不要用。稍后您将看到完整的解决方案比单独的模板要短得多。

## 解

我们将从实际的`solve`函数开始，该函数将巧克力条作为一个整数列表，包括生日和月份。我们从编写类型签名开始。

```
solve :: [Int] -> Int -> Int -> Int 
```

这里`solve`是我们函数的名字，双冒号后面是函数的类型。每个箭头代表一个函数，箭头右边的都是函数返回的结果。这个函数接受一个整数列表，然后返回一个接受整数的函数，以此类推，直到它只返回一个整数。这是因为 Haskell 中的每个函数都只有一个参数(这叫做 currying)。实际上这并不重要，因为语法非常方便。我们继续写实现的开始，从命名我们的函数参数开始。

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = 
```

看到了吗？你甚至没有注意到 solve 返回了一个函数！但是，如果我们想调用这个函数，我们可以这样做:

```
fn1 = solve [1,2,3,4] -- fn1 takes an integer and returns a function that takes an integer
fn2 = solve [1,3] 4 -- fn2 takes an integer and returns an integer
x = solve [1,3] 3 4
x' = fn1 2 4 -- I can give the rest of the arguments later 
```

现在，回到问题上来。我们想把长条切成长度等于你朋友出生月份的薄片。为此，我们为自己编写了一个小的帮助器函数，它检查条形是否足够长，可以生成一个新的切片，如果足够长，就再次递归调用自己。否则它只会返回一个空列表。在 Haskell 中，我们可以用关键字`where`将帮助函数添加到函数定义中。

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = {- stuff missing here -} slice bar
    where slice b
            | length b >= m = take m b : slice (tail b)
            | otherwise     = [] 
```

在这里，我们使用所谓的“保护”。我们将定义一分为二，只有当等号前的条件满足时，我们才计算右边。如前所述，首先我们检查是否可以制作另一个切片，如果可以，我们从`b`中`take` `m`元素。正如您已经猜到的，`take`返回列表`b`中第一个`m`元素的列表。然后我们使用`:`将新列表添加到`slice`返回的列表中。`slice`的参数在输入中使用了`tail`，这将返回没有第一个元素的列表。

让我们手动演示一下这个功能。函数式编程的好处在于，您可以用它们的实现替换定义，以查看评估的内容。

```
m = 2
b = [2,2,1]
x = slice b -- length b is greater than 2, so we use the first definition
x = take 2 b : slice (tail b) -- replace tail and take with their results
x = [2,2] : slice [2,1] -- replace slice with definition, still the first one
x = [2,2] : (take 2 [2,1] : slice (tail [2,1])) -- replace tail and take again
x = [2,2] : ([2,1] : slice [1]) -- replace slice again, this time the second definition
x = [2,2] : ([2,1] : []) -- prepend list to empty list
x = [2,2] : [[2,1]] -- do again
x = [[2,2], [2,1]] -- now we got a list of list, with the possible slices 
```

现在我们只对总和等于出生日期的那些切片感兴趣。为此我们可以`filter`列举。

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = filter fn $ slice bar
    where slice b
            | length b >= m = take m b : slice (tail b)
            | otherwise     = [] 
```

Haskell 中的`$`操作符是为了避免括号。它首先计算右边的东西，然后把它作为左边的参数。`foo $ bar x`与`foo (bar x)`相同。

目前，我们还没有定义这个`fn`。我们可以写另一个帮助函数，但是我们没有必要这样做。我们可以组合现有的函数来形成我们需要的函数。让我们回顾一下函数应该做什么:Filter 将遍历切片列表中的所有列表，因此我们的函数将接收单个切片。首先我们要对列表求和，然后我们要检查这个值是否等于出生日期。

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = filter ((== d) . sum) $ slice bar
    where slice b
            | length b >= m = take m b : slice (tail b)
            | otherwise     = [] 
```

就是这样！在 Haskell 中，操作符也只是函数，所以我们也只能提供它们的一部分参数。我们在这里做的是:`(== d)`返回一个函数，检查输入是否等于`d`。这个输入来自于`sum`函数，该函数获取一系列数字并将它们相加。

最后缺少的是计算有多少这样的切片。因为我们只需要过滤列表的长度。

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = length $ filter ((== d) . sum) $ slice bar
    where slice b
            | length b >= m = take m b : slice (tail b)
            | otherwise     = [] 
```

在这里你也可以看到:组成(T0)和应用(T1)是直接相关的。这取决于你的观点。目前，我们的观点是:“评估条形切片，然后将结果应用于过滤函数，然后将结果应用于长度函数”。但是我们可以很容易地改变我们的观点:

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = length . filter ((== d) . sum) . slice $ bar
    where slice b
            | length b >= m = take m b : slice (tail b)
            | otherwise     = [] 
```

现在我们的观点是:“将 bar 应用于用 filter 和 slice 合成 length 的结果的函数”。代码仍然是一样的，但是(至少在我看来)意思有点变了。

## 使其可执行

我们的解决方案的核心已经完成，但现在我们需要处理来自标准输入的数据，并将结果打印到标准输出。在 Haskell 中，每个程序的入口点都是`main`。它不是一个函数，而是一个事实上的值。因为在 Haskell 中 IO 也是一个可以传来传去的值！

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = -- omitted

main :: IO ()
main = putStrLn "Hello" 
```

这里，`putStrLn`是接受一个字符串并返回这样一个`IO`值的函数。请注意，在类型签名中，括号不代表函数，而是代表空元组，也称为单元。它就在那里，因为`IO`需要“包含”一个值，所以我们给它单位。

但是我们不想要一个接受字符串并执行 IO 的函数，我们想要一个从 stdin/stdout 读写的函数，让我们只处理字符串。这个还有一个函数:`interact`。它从一个字符串到另一个字符串传递一个函数并返回 IO。

```
interact :: (String -> String) -> IO () 
```

所以现在我们必须创建一个函数，从上面的输入格式中获取一个字符串，并返回一个带有结果的字符串，在两者之间调用我们的`solve`函数。我们将一步一步地再次使用函数组合。首先，我们希望将输入拆分成单独的行。为此我们可以使用`lines`函数，它接受一个字符串并返回一个字符串列表。然后我们对第一行不感兴趣，所以我们可以使用前面的`tail`来删除该行列表中的第一个元素:

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = -- omitted

main :: IO ()
main = interact $ tail . lines 
```

现在*对于每一行*(也就是列表中的元素)，我们想要首先将字符串拆分成单词，然后*对于每个单词*，我们想要将字符串转换成整数。每当我们想要转换 Haskell 中列表中的每个元素时，我们就使用`map`函数。它接受一个函数和一个值列表，并返回一个列表，其中函数应用于列表中的每个元素。

```
map :: (a -> b) -> [a] -> [b]
------
x = map (+1) [1,2,3] -- x is [2,3,4] 
```

您会看到类型签名使用小写字母，而不是像前面那样使用大写字母。这意味着这是一个类型变量，所以你可以把任何你想要的类型放进去。现在让我们把上面的句子转换成一个函数:

```
solve :: [Int] -> Int -> Int -> Int
solve bar m d = -- omitted

main :: IO ()
main = interact $ map (map read . words) . tail . lines 
```

外部的`map`将为它接收的每一行应用内部函数。内部函数首先将一行拆分成单词，然后将`read`应用于单词列表中的每个元素。

此时，我们的函数获取一个字符串并返回一个整数列表，其中列表中的第一个元素是横条(或表示横条的列表)，第二个元素是由您朋友的出生日期和月份组成的列表。现在我们可以调用`solve`，但是参数不匹配，solve 不需要列表的列表，而是三个参数。因为我们总是知道列表的列表会是什么样子，所以我们可以使用模式匹配从列表中提取参数。

```
solve :: [[Int]] -> Int
solve [bar, [m, d]] = -- omitted, did not change

main :: IO ()
main = interact $ solve . map (map read . words) . tail . lines 
```

你可以看到我们首先在外部列表中匹配，命名第一个元素`bar`。然后我们进一步匹配第二个元素，并将它命名为`m`和`d`。我们还必须相应地调整我们的类型签名。在生产环境中，您将在不同的函数中分解模式匹配，因为原始的类型签名`solve`比新的更有意义。但由于这只是一个编码挑战，我们不会费心去做。

需要的最后一步是将从`solve`返回的整数转换回字符串(记住`interact`需要一个从字符串到字符串的函数)。为此，haskell 中有一个函数`show`。

```
solve :: [[Int]] -> Int
solve [bar, [m, d]] = length . filter ((== d) . sum) . slice $ bar
    where slice b
            | length b >= m = take m b : slice (tail b)
            | otherwise     = []

main :: IO ()
main = interact $ show . solve . map (map read . words) . tail . lines 
```

至此，我们的解决方案就完成了！

## 关闭思绪

正如你所看到的，当使用函数式语言时，你不是试图告诉计算机如何做某事，而是告诉 T2 你想做什么。这也是函数组合比函数应用更受青睐的原因之一(当然在情理之中)。

我真的很喜欢每天写 Haskell，但是我知道这是一门令人畏惧的语言。我希望这个系列能让“掌握”这门语言变得更容易一些。感谢您的阅读。