# Haskell 中的 Currying(用一些 JavaScript)

> 原文：<https://dev.to/nestedsoftware/currying-in-haskell-with-some-javascript-4moj>

最近我一直在钻研函数式语言 [Haskell](https://www.haskell.org/) ，我发现它有一种有点不寻常的处理函数参数的方式。通常，你提供参数并调用一个函数，这就是故事的结尾。

例如，下面这个简单的 JavaScript `sub`函数只是减去了它的两个参数:

```
const sub = (first, second) => first - second 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样称呼它:

```
sub(7,2) 
```

Enter fullscreen mode Exit fullscreen mode

让我们用 Haskell 写`sub`，看看它和 JavaScript 版本有什么不同:

```
main = print (sub 7 2) 

sub :: (Num a) => a -> a -> a
sub first second = first - second 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看结果:

```
C:\dev>ghc sub.hs
[1 of 1] Compiling Main             ( sub.hs, sub.o )
Linking sub.exe ...
C:\dev>sub.exe
4 
```

Enter fullscreen mode Exit fullscreen mode

这看起来好像是同一个功能。签名好像在说:取两个数字作为参数，返回第三个数字作为结果。但是，请注意`a -> a -> a`中是如何没有括号的？人们可能会期待更像`(a, a) -> a`的东西。这实际上是一个线索，表明有些稍微不同的事情正在发生。

下面我试着想出一个方法来展示这一点:

```
main = print finalresult
    where finalresult = partialresult 3
          partialresult = sub 7 
```

Enter fullscreen mode Exit fullscreen mode

如果我们像上面那样修改 main 函数，我们可以看到，调用只有一个参数`7`的 sub 返回一个函数。我们用`3`调用这个中间函数，然后它返回`4`，减法的实际结果。

> 每次函数返回时，它都保留对传递给调用函数的参数的访问。能够像这样保留封闭范围的函数，即使执行已经移出了与该范围相关的块，也称为 [*闭包*](https://en.wikipedia.org/wiki/Closure_(computer_programming)) 。

那么，到底发生了什么？实际上，`sub`函数以单个数字作为参数，返回一个函数。该函数也接受一个数字作为参数，并返回减法的结果。这种将接受多个参数的函数分解成每个函数只有一个参数的嵌套函数的想法被称为*。*

 *让我们试着用 JavaScript:
来模拟这种行为

```
const sub = first => {
    const intermediateResult = second => {
        return first - second
    }

    return intermediateResult
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是我们如何在 JavaScript 中调用这种类型的函数:

```
const result = sub (7) (3) 
console.log('subtraction result = ' + result) 
```

Enter fullscreen mode Exit fullscreen mode

我们用`7`作为参数调用`sub`，然后用`3`调用它返回的函数。这个中间函数实际上是计算两个值之差的函数。

在 Haskell 中，currying 是语言的一部分。Haskell 中的任何函数都可以用部分参数调用，其余的参数可以在以后应用。

阿谀奉承有用吗？

```
map (+3) [1,5,3,1,6] 
```

Enter fullscreen mode Exit fullscreen mode

在 Haskell 中，我们可以只使用一个参数调用`+`函数，在本例中为`3`。`map`然后调用中间函数，将列表中的每一项作为参数。

在像 JavaScript 这样的东西中，我们不能直接这样做，但是我们可以用 lambda:
很容易地解决这个问题

```
[1,5,3,1,6].map(x=>x+3) 
```

Enter fullscreen mode Exit fullscreen mode

虽然在我看来，谄媚并不像[一级函数](https://en.wikipedia.org/wiki/First-class_function)和[闭包](https://en.wikipedia.org/wiki/Closure_(computer_programming))那样是函数式编程的本质，但我不得不承认 Haskell 处理参数的方式有一定的正交性和概念上的纯粹性。

特别是，currying 很好地符合 Haskell 中的大多数东西都被[延迟评估](https://en.wikipedia.org/wiki/Lazy_evaluation)的事实。在这种情况下，currying 有一定的意义，因为大多数函数无论如何都会计算出一个 *thunk* ，并且底层逻辑直到需要一个完整的结果时才会被完全处理。

如果你有兴趣学习更多关于 Haskell 的知识，我强烈推荐你从教程[开始学习 Haskell！](http://learnyouahaskell.com/)。*