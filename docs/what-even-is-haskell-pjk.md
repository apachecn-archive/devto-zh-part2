# 哈斯克尔到底是什么？

> 原文：<https://dev.to/ashe/what-even-is-haskell-pjk>

你可能已经注意到我最近一直在谈论 Haskell。在这篇文章中，我想用通俗的语言写一个初学者友好的总结，关于我为什么对一门我才开始使用的语言如此感兴趣。希望这能激励一些人考虑使用他们已经习惯的替代语言。

## Haskell 是什么？

> 摘自 [wiki](https://wiki.haskell.org/Introduction) 、*、 [Haskell](https://haskell.org) 是一种[多态](https://wiki.haskell.org/Polymorphism)、[静态类型化](https://wiki.haskell.org/Typing)、[懒惰](https://wiki.haskell.org/Lazy_evaluation)和[纯功能性](https://wiki.haskell.org/Functional_programming)语言*。

希望在这篇文章结束时，这句话会被一点一点地分解。我开始想学习 Haskell 的原因之一是因为人们向我保证，学习 Haskell 将从总体上提高你的编程技能。出于我们将要探讨的原因，Haskell 有一个相当陡峭的学习曲线，但是有巨大的回报。一旦你能理解它，你不仅能在 Haskell 中产生优雅的代码，而且你还能思考对函数式编程很重要的新概念，这些概念也适用于其他语言。需要注意的是，虽然 Haskell 是一种纯粹的函数式语言， ***一些语言比如 JavaScript 也能够是函数式的*** ，所以即使你不打算使用 Haskell，理解 FP 也是很好的，因为它可能会在你以后使用的语言中对你有所帮助。

### 功能编程

让我们通过几个例子从函数式编程实际上是什么开始。本质上，函数式编程是从[命令式](https://en.wikipedia.org/wiki/Imperative_programming)编程(大多数人习惯的编程)的一种思维转变，其中额外的重点放在通过函数对数据的操作和转换上。更像 Haskell 或 FP 专家的人可能会给出更好的答案，但对于像我过去的自己这样的人，我会给出这样的解释。当你没有真正使用过任何函数式语言时，很难想象这两者之间的区别，所以试着跟上，希望我可以相对轻松地做到这一点。

为了解释我上面提到的，我想开始说我们说‘功能’的意思。在数学方面，你可以想象他们早期使用的‘数字机器’教学方法。您将一些数字或某种形式的数据放入函数中，它会吐出不同的数字或数据。然而，当你开始学习编程时，你很快就忘记了这个想法，而是把函数想象成一组指令——你是在告诉计算机按照操作执行的顺序做一些事情。有循环，有 if 语句，但最终还是有一些明确的指令集，是你的程序所遵循的。一个游戏的整体是在一个循环中，这个循环一直持续到游戏结束——你通过运动，角色移动的每一帧，世界都做出反应，最后世界被绘制出来。

当我们谈论命令式编程和函数式编程时，忘记[面向对象](https://en.wikipedia.org/wiki/Object-oriented_programming)和[过程式](https://en.wikipedia.org/wiki/Procedural_programming)编程等概念。我们不是在谈论用一种语言编程的方式，我们是在谈论这种语言本身能够做的编程的*类型*。他们是独立的，所以你不应该在这篇文章中纠缠他们。我只是想澄清一下，以防你在命令式语言中进行过程式编程时，开始质疑过程式和命令式之间的区别。

回到正题。函数式编程是一种完全不同的游戏。由于事情的处理方式，很难将命令式代码转换成函数式代码。关于这个主题有大量的信息和讨论，所以我不会说得太详细，而是试图浏览最简单的方面，以便没有尝试过的人仍然可以理解它。下面是命令式编程的一个例子:

```
// A class in C# which will represent the player in the game:
public class Player {

    // The player's current location
    Vector2 position = Vector2(0, 0);

    // Function to move the player
    public void MovePlayer(int x, int y) {

        // Ensure that the player will always be in-bounds (0-1000)
        if (this.x + x > 1000)
            this.x = 1000;
        else if (this.x + x < 0)
            this.x = 0;
        else
            this.x += x;

        // Imagine the same for Y:
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以在这个例子中，我们有一个非常简单的类`Player`，它只有一个函数`MovePlayer`。这个函数绝对不返回任何东西，它是一组详细说明玩家如何响应某种形式的输入的指令。当我们像这样调用函数:`MovePlayer(1, 2);`当程序刚刚开始的时候，我们应该期望播放器的坐标是`(1, 2)`假设其余的函数都完成了。呼叫它两次将移动玩家到`(2, 4)`等等。这是[面向对象编程](https://en.wikipedia.org/wiki/Object-oriented_programming)的一个简单例子，非常有效，同时也易于阅读。

看`MovePlayer`的时候，你从顶部开始往下读——为什么不呢？这感觉很自然，它实际上看起来像一个指令列表-你可以像电脑一样进行角色扮演，一步一步地在你的脑海中做所有的事情。当浏览`if`语句时，很容易看到函数的流程以及下一步执行哪一行。

是时候引入一些 FP 了。事实上，这个函数不返回任何东西(嗯，`Void`，因为你实际上可以在 Haskell 中返回`Nothing`！)表明这个函数“做”了一些事情，它不会以任何方式转换你的数据，它只是将数据存储起来并累积起来。这个函数的结构根本不能清楚地转换成函数式语言。Haskell 中只有几个函数“做”事情——从某种意义上来说，可以认为一个函数是不纯的，它将使用所有的纯函数来完成事情。

在命令式编程中，状态随处可见。玩家类的每个实例都有一种“状态”的感觉，因为每个实例都保存着详细描述它们在任何时刻所处位置的数据。在 Haskell 中，没有进入高级技术，就不再有真正的状态感。我们将在下一章讨论纯度的含义，所以我们将忽略状态感(这个`Player`类),而是看一个`MovePlayer`起作用的函数。

```
-- A function in Haskell:
calculateNewCoords :: (Int, Int) -> (Int, Int) -> (Int, Int)
calculateNewCoords (x, y) (i, j) = (calc x i, calc y j)
    where calc a b
            | a + b > 1000 = 1000
            | a + b < 0 = 0
            | otherwise = a + b 
```

Enter fullscreen mode Exit fullscreen mode

现在，对于那些不熟悉 Haskell 的人来说，可能会对上面的代码感到有点害怕。这当然可以*变得更加优雅，但是我觉得这个版本展示了 Haskell 的很多特性。首先，我们定义了一个叫做`calc`的“迷你函数”。我们不再从上到下阅读函数，因为为了理解第二行，我们需要先阅读`calc`。Haskell 是非常基于数学的，所以你需要想象你正在写一个数学函数，而不是一组指令。在这种情况下，我们定义了一个函数，该函数研究两个向量并将它们的分量相加，并确保它们在 0 和 1000 之间。显然，如果边界有任何不同，我们要么需要硬编码它们(通过替换`1000`和`0`)要么通过传递它们。*

其次，让我们解决第一行实际上意味着什么。第一行是函数的类型声明——在本例中，我们传入两个元组(在本例中是对),每个元组包含两个`Int`,然后最终返回一个元组。如你所料，这个函数用一个向量来表示 ***、一个位置*** 和一个用于平移的向量。参数由`->`分割，其中最后一部分是返回值。通过遵循我们在前一个例子中建立的规则，这个函数应该有希望吐出另一个`Int`的元组，这个元组的位置最终将会在那里。我强调它是一个位置的原因是因为这个函数不属于任何类或实例；什么都可以叫它。因为我们传入我们正在操作的位置并返回结果，所以我们能够称之为一个纯粹的*函数，它完全不受副作用的影响，并且不依赖于要调用的任何其他函数。如果你对[并行计算](https://en.wikipedia.org/wiki/Parallel_computing)感兴趣，这是一件非常好的事情，因为这意味着两个函数不能同时接触相同的数据。*

 *根据上一点——你总是在操作和返回数据的事实意味着 Haskell 和其他函数式语言的另一个优势是数据是不可变的 T2；数据一旦创建，就不能更改。如果你正确和明智地使用数据，这可以节省内存。它也更安全——除非你覆盖你的变量(这通常是用`do`语法完成的，有点超出了本博客的范围)，你的数据将总是相同的。在 Haskell 中你通常不会大量覆盖数据，但是如果你这样做了，那就很像覆盖一个通过值传递的变量，你只是在弄乱副本。

### 纯函数

让我们仔细看看`Pure`的功能。如前所述，纯功能无副作用。所谓副作用，我们指的是调用一个函数的结果。这可能是改变不属于函数的值，或者只是使用它们。这里有一个祈使句示例:

```
// C# Example 1 -
// This function can be called from anywhere:
public int addem(int a, int b) {
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个用 C#编写的非常简单的函数。当你可以使用`+`使这个函数变得完全多余的时候，不需要太多就可以意识到这个函数没有多大意义。让我们稍微混合一下。

```
// C# Example 2 -
// Imagine this is a function in some sort of class to store 'c':
int c = 5;
public int addem(int a, int b) {
    return a + b + c;
} 
```

Enter fullscreen mode Exit fullscreen mode

那么这两个函数有什么不同呢？嗯，再一次，不需要很高的智商就能看出现在有一个名为`c`的外部变量，它将被添加到我们传递给函数的任何变量中。这里需要注意的是，只要`c`在函数外部可见，传入`a = 1`和`b = 2`并不总是返回`8`。这就是单词`pure`的来源:一个纯函数将 ***总是*** 为相同的给定输入集合返回相同的输出。在进行函数式编程时，大多数函数都将遵循这种模式。如果你是一个更投入的程序员，你现在应该感到很苦恼——你到底是怎么完成任何事情的？

答案是把你的程序分成纯的和不纯的部分。您开始查看哪些计算实际上需要状态感，并将您的功能过滤成小的、专注的功能，这些功能只专注于操作数据。当你包装东西的时候，你可能会有几个或者仅仅一个不纯的函数把所有的东西联系在一起。FP 的关键是真正评估每一个函数的纯度，保证你能有尽可能多的纯函数。

### 懒惰

现在是时候看看 Haskell 的描述中标记的其他流行语了。[惰性求值](https://wiki.haskell.org/Lazy_evaluation)意味着你实际上可以利用无限长的列表而不会使程序崩溃。Haskell 只会在需要的时候计算表达式，所以从一个无限列表中取第 32132 个数字只需要列表计算到那一点。下面是一些用不同方式编写的无限列表的例子:

```
-- Haskell time:
-- This is a list of one through ten
λ> [1..10]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

-- The same list as above, but with a 'step' to make it odd only
λ> [1,3..10]
[1, 3, 5, 7, 9]

-- This is a list like the first but doesn't stop at ten
λ> [1..]

-- Being able to create infinite lists easily like this simply relies
-- on the 'Ord' typeclass. Basically, if you have a type that can be
-- ordered, you can make infinite lists like we've shown here!

-- Note that it doesn't exist until you actually use it
λ> [1..] !! 5
5
λ> take 5 [1..]
[1, 2, 3, 4, 5]

-- A list like the second, but infinite
λ> [1, 3..]

-- Now when we use the lists
λ> [1, 3..] !! 5
9
λ> take 10 [1, 3..]
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19]

-- Going down
λ> take 10 [5, 4..]
[5, 4, 3, 2, 1, 0, -1, -2, -3, -4]

-- Creating an infinite list through recursion
λ> let someList = 1 : map (+1) someList
-- Every iteration prepends '1' and then maps (+1) onto every element in the list as follows:
-- [1]
-- 1 : [2]
-- 1 : [2, 3]
-- 1 : [2, 3, 4]

-- Using the recursive endless loop will not break
λ> take 5 someList
[1, 2, 3, 4, 5]
λ> take 5 [x*2 | x <- someList]
[2, 4, 6, 8, 10]

-- Try to work out how this infinite list is computed, what number is next?
-- This is called 'List Comprehension'
λ> take 12 [x*3 + y | x <- someList, y <- [10,9..7]]
[13, 12, 11, 10, 16, 15, 14, 13, 19, 18, 17, 16]

-- Here is a filter with list comprehension
λ> take 5 [x | x <- [5, 6..], x `mod` 2 == 0]
[6, 8, 10, 12, 14]

-- Ready to get your mind blown?
λ> take 5 [if x `mod` 2 == 0 then "EVEN!" else "ODD!" | x <- [1..]]
["ODD!","EVEN!","ODD!","EVEN!","ODD!"]

-- Note that I've taken out the floating point errors here
λ> take 5 [0, 0.1..]
[0.1, 0.2, 0.3, 0.4, 0.5]

λ> take 5 ['l'..]
['l', 'm', 'n', 'o', 'p']
-- Actually, in Haskell, a string is actually just a list of characters, so..
"lmnop"

-- Note that the above example isn't constrained to the alphabet
-- and will never loop back round to the alpha-numeric ascii characters
-- Not to worry though! A function called cycle can loop a list infinitely!
λ> ['A'..'Z']
"ABCDEFGHIJKLMNOPQRSTUVWXYZ"

λ> let alphaBetty = cycle ['A'..'Z']
-- Now alphaBetty is a list of the alphabet cycling infinitely
λ> take 6 (drop 23 alphaBetty)
"XYZABC"

-- Note that cycle doesn't require Ord at all, as it uses a pre-made list

-- Also note, that [1..] is simply syntactic sugar for the method we used for
-- 'someList' 
```

Enter fullscreen mode Exit fullscreen mode

我猜你可能会说我在这方面很懒，因为我只是演示了无限列表，而不是真正讨论它们，但是谁在乎呢？

### 静态打字

这个主题不是 Haskell 特有的，但是我还是会给你一个大概的介绍，因为你可能一开始就不清楚。本质上，在[静态类型](https://wiki.haskell.org/Typing)中，每个变量的类型在编译时是已知的。通常，这意味着程序员必须明确说明变量的类型，然后才能使用它。这在 C++中显而易见:

```
// C++:
int sum;
int number = 0;
sum = number + 1; 
```

Enter fullscreen mode Exit fullscreen mode

在 Haskell 中，有一种叫做“类型推理”的东西，这意味着你不必定义所有的变量类型，因为这种语言非常智能。尽管如此，您仍然应该显式地标记您的函数，以确保编译器与您处于同一阶段。C++和 Haskell 都是静态类型语言，包括(但不限于)Java、C 和 Scala。

那么什么是*动态打字*？这与静态类型相反。JavaScript 和 Python(一种我不熟悉的语言)都是动态类型语言的例子。例如，你不必写出你的变量是一个`Int`。请记住，一些静态类型的语言能够做到这一点，但这只是编译器聪明地为您做了工作。人们说 JS 是“非类型化的”,但我将称之为动态类型化，因为它绝对不是静态的。现在，冒着让你困惑的风险，我将从[强类型和弱类型](https://en.wikipedia.org/wiki/Strong_and_weak_typing)的角度来讨论动态类型。这是两个不同的概念，所以你不应该把它们混为一谈——仅仅因为一种语言是动态类型的并不一定意味着它是强(或弱)类型的。

如果你使用的是强类型语言，那么一旦你将一个变量绑定到一个类型上，你就必须确保它总是那个类型。例如，将一个`String`和一个`Int`连接起来可以吗？`"High " + 5`能编译吗？如果有，有没有一个显式的方法，或者是你的语言的一个特性，允许类型像这样混合？弱类型语言将很容易允许它，导致`"High 5"`。这是一个非常简单的例子，也不是很重要，但是不提到它就很难谈论动态类型。

### 多态性

不，不是魔兽世界里的[法术，然而，这两者背后的含义是结伴而行的。本质上，](http://www.wowhead.com/spell=118/polymorph)[多态](https://wiki.haskell.org/Polymorphism)允许一个变量有不止一种类型。一旦类型被绑定，那么它必须在每次调用所述变量或参数的过程中保持一致，但在此之前，它可以是任何你喜欢的合理的东西。Haskell 有多种方法可以做到这一点，第一种非常容易演示。如果你听说过身份函数，那么这很快就会有意义:

```
-- Haskell's identity function:
id :: a -> a 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在 Haskell 中，我们将`::`理解为“*具有类型*”,因为在“*中，函数`id`具有类型`a -> a`* ”。这并不是说函数实际上做了什么，然而，考虑到类型，它*能做的事情并不多。那么什么是`a`？嗯，我们不知道——它是 ***多态*** 。因为我们不知道它实际上是什么或者可能是什么，我们不能真正地把它传递给任何函数*，除非*函数也接受一个参数`a`，这意味着它接受任何东西。`a -> a`表示该函数将接受任何类型的变量，并将返回相同类型的变量*。如果你不确定什么是恒等函数，它只是一个在上下文中对参数没有任何作用的函数。`1`是乘法和除法的恒等式，因为任何`*1`或`/1`都等于自身- `x * 1 = x`。另外(还有减法)，`0`是恒等式因为`x + 0 = x`。在这种情况下，`id`是一个恒等函数，它对其参数不做任何处理，只是返回它们。现在它可能没有意义，但它确实有它的用途。**

 *总之，`id`是一个多态函数，因为它接受字面上的 ***任何*** 类型。它不需要任何关于它接受的东西是什么的信息——它可以是一个列表，甚至是另一个函数——那是另一篇博文的内容；).该函数将总是返回相同类型的内容，并且只要您传入一个参数，该函数就会被绑定。这里有一个例子:

```
-- Usage of id in Haskell:
λ> id 1
1

-- The type of that first id call was Int -> Int
-- ... or was it? We'll get onto that next!

λ> id "hello"
"hello"

-- The type of id here was [Char] -> [Char], or String -> String
-- as string is essentially an alias of [char]

λ> id ['A', 'B', 'C']
"ABC"

-- This third call is exactly the same as the example above,
-- Haskell prefers to specify that it's a [Char], but then
-- writes it as a string to be friendly to read for us, thanks HS!

λ> id [(1, "Hello"), (2, "there"), (3, "mate!")]
[(1, "Hello"), (2, "there"), (3, "mate!")]

-- The type of id here in the fourth call was
-- [(Int, [Char])] -> [(Int, [Char])]
-- Again, not really Int, but that's coming up next 
```

Enter fullscreen mode Exit fullscreen mode

正如我们之前所经历的，`->`符号将参数彼此分开，其中最后一行是返回值。`id`的类型绑定到我们传入的任何变量类型。同样重要的是要记住`a`很容易使用，因为它是字母表的第一个字母，但是名字并不重要。类型以大写字母开头，如`Int`、`Bool`、`Char`或`String`。任何小写字母都可以被视为任何类型的变量。`a -> b`意味着我们有一个函数返回一些东西，这些东西*可能与我们传入的类型不同。`a -> b -> c`意味着我们传入两个不一定是同一类型的参数，并得到另一个可能不同的类型，或者与`a`或`b`相同。记住，这些字母可能是任何东西。仅仅因为`a`和`b`表明在我们的函数中有两种独立类型的使用，并不意味着它们*有*不同，只是它们*可能有*不同。*

 *那么我在说什么，当然第一个电话是`Int -> Int`，我搞定了那个阿什利！我猜到了！嗯，在这种情况下，你实际上有点太具体了。是时候在类型声明的上下文中引入`=>`了。

```
--Haskell identity function again:
λ> id 1
1
λ>:t id 1
Num a => a 
```

Enter fullscreen mode Exit fullscreen mode

因此，当我们在终端中使用 Haskell 来计算表达式时，我们可以使用`:t`来查询某种东西的类型。这是查询`id 1`的*结果*的类型，所以不要搞混了。我们的答案是`Num a => a`，这与我目前展示的略有不同。`=>`用左边的定义描述右边的东西——意思是箭头后面的`a`正在派生 typeclass `Num`。正如我所说的，我们要求函数的*结果*，所以我们的编译器告诉我们结果是`a`，其中`a is part of the Num`是 typeclass。因此，在函数被应用之前，我们的函数`id` *的专门化类型变成如下:* 

```
Num a => a -> a 
```

Enter fullscreen mode Exit fullscreen mode

您仍然可以看到原始的`a -> a`类型声明通过，但是现在我们知道发送进来的`a`和检索到的`a`是同一个类型，现在是一个`Num`。什么是`Num`？Haskell 的第二种多态方式没有前一种那么通用。随着你变得越来越具体，你会以通用为代价获得更多的功能——这真的很有意义，因为你不能在没有定义一些基本原则的情况下将功能附加到某些东西上。

输入`Num`，一个数字的 typeclass。当某个东西派生出`Num`时，我们的意思是不管它是什么类型，它都必须有一些核心功能。这么说吧，当你派生`Num`时，你必须为你的类型填充一些函数，展示它们是如何操作的——如果你创建了一些派生`Num`的古怪类型，你需要弄清楚像`+`、`-`和`*`这样的东西是如何工作的(你可以研究为什么不定义`/`)。这些功能可以在 Hackage 上找到——只要确保你让页面加载并滚动到`Num`部分，否则你会搞混的。让我们来看看该页面的功能:

```
-- Haskell Num functions:
λ> :t (+)
(+) :: Num a => a -> a -> a
-- If you have followed everything so far, you should
-- be able to see that addition takes two Nums and returns a Num

λ> :t (*)
(*) :: Num a => a -> a -> a
-- Remember, we are working with Num here, not Int
-- We don't know how this function is implemented, just that types
-- like Int are compatible with these functions.

λ> :t abs
abs :: Num a => a -> a
-- Note that here we only have one parameter and one return value
-- that's because getting the absolute value simply makes the number positive
-- and it doesn't need any other parameters to operate

-- Let's query what typeclasses Int actually derives, so you can see how much Int can do!
λ> :i Int
data Int = GHC.Types.I# GHC.Prim.Int#   -- Defined in `GHC.Types'
instance Bounded Int -- Defined in `GHC.Enum'
instance Enum Int -- Defined in `GHC.Enum'
instance Eq Int -- Defined in `GHC.Classes'
instance Integral Int -- Defined in `GHC.Real'
instance Num Int -- Defined in `GHC.Num'
instance Ord Int -- Defined in `GHC.Classes'
instance Read Int -- Defined in `GHC.Read'
instance Real Int -- Defined in `GHC.Real'
instance Show Int -- Defined in `GHC.Show' 
```

Enter fullscreen mode Exit fullscreen mode

从上一个演示中可以看出，`Int`实际上有很多功能！如果我们有一个函数，它有一个像`Eq a => a -> a -> Bool`这样的声明，我们可以开始考虑它可以做的事情的类型。`Eq`是平等的典型——在检查事物是否平等方面。如果我们有一个函数，它采用两个相同类型的变量，这两个变量能够进行比较，以确定它们是否相等，我们可以看到如何从中获得一个`Bool`(其中`Bool`是`True`或`False`)。这个函数仍然是多态的，因为我们不知道我们得到的是什么类型，我们只知道它们可以比较。它不是 100%通用的，但也不是 100%特定的，因此符合多态的条件。

您可能已经知道您当前的语言是如何实现多态性的，是通过继承其他类还是其他方法。Haskell 就是这么做的，它非常， ***非常*** 强大。

## 把东西包起来

我希望这个小冒险是一个有趣的阅读，而且我真的说服你们中的一些人稍微深入 Haskell。学习 Haskell 对我渴望挑战的大脑有很大的帮助，我爱上了数学的一切。如果你有兴趣开始学习，可以看看免费的[Learn you a Haskell for a Great Good(LYAH)](http://learnyouahaskell.com/)，或者更全面、更复杂、更昂贵的 [Haskellbook](http://haskellbook.com/) 。如果你想认真对待，去找 [Haskellbook](http://haskellbook.com/) ，但是我建议你先看看 [LYAH](http://learnyouahaskell.com/) 来快速概述你能做什么。它将使您能够开始运行代码，但是更多的学者可能需要更多的东西。

如果你还想看别的东西，可以去 YouTube 上的 ComputerPhile 看一看，他们做了一些很棒的视频，不仅仅是关于 Haskell，也不仅仅是关于函数式编程，而是关于一般计算。不过一定要看看 Haskell 的视频。如果你需要一些帮助开始(甚至当你是一个专家！)或者想和一些 Haskell 专家交流，可以去 Freenode 上的[# Haskell IRC](http://webchat.freenode.net/?channels=haskell)或者[Functional Programming Discord Server](https://discord.me/fp)。

最后，给我一个[关注](https://twitter.com/crysikrend)和一条推文，如果这有帮助的话。我正在尽我所能把我所知道的分享给那些渴望学术的志同道合的人。

## 感谢阅读，写这个真的很有趣！***