# 使用以下任一方法处理错误

> 原文：<https://dev.to/avalander/handling-errors-with-either-2i7j>

一个`Either`基本上是一个可能是错误的值的容器。有了`Either`,我们可以对包含的值应用转换，而不必担心它是否是一个错误，直到我们在代码中到达我们想要处理错误的点，如果错误已经发生的话。这有点像[薛定谔的盒子](https://en.wikipedia.org/wiki/Schr%C3%B6dinger%27s_cat):值可能是也可能不是错误，我们直到打开它才会知道(好吧，错过了薛定谔的猫的要点，但我还是想放参考文献)。

# 二者都是如何工作的？

为了说明`Either`的结构，让我们用 Javascript 构建它。

首先，一个`Either`可以保存一个值或者一个错误。我们将分别称他们为`Right`和`Left`。在某种意义上，这就像有两个分支，如果你得到一个错误，你要么去左边，要么去右边，如果你得到一个有效的值。

此外，我们需要能够将转换应用到`Either`中的值。不然真的没啥用。我们需要一个`map`函数来完成这项工作。我们将只在`Right`分支上应用转换，如果有`Left`就忽略它。

```
const Left = x => ({
    map: fn => Left(x),
})

const Right x => ({
    map: fn => Right(fn(x)),
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意，`Left.map`返回一个保持相同值的`Left`，而不应用变换`fn`，而`Right.map`返回一个包含将`fn`应用于该值的结果的`Right`。这样做的原因是，我们只想对有效值进行转换，而不是对错误进行转换。

```
Right(3).map(x => x * x) // -> Right(9)
Left(3).map(x => x * x) // -> Left(3) 
```

Enter fullscreen mode Exit fullscreen mode

现在假设我们想要对包含在`Either`中的一个值应用一个转换，但是这个转换会返回一个错误。既然我们用`Either`处理错误分支，我们也可以返回一个新的`Either`。

```
const result = Right(3)
    .map(x => x % 2 == 0
        ? Right(x)
        : Left('Odd')) 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个包含在`Either`中的数字，我们只想接受偶数。如果是奇数，我们返回一个`Left`表示数字是奇数。

问题是现在我们有一个包含在一个`Right`中的`Left`。如果我们检查变量`result`，它将保持`Right(Left('Odd'))`。如果我们想应用另一个变换，我们应该将它应用于外部的`Right`还是内部的`Left`？当下一个转换返回另一个`Either`时会发生什么？

为了解决这个问题，我们可以实现方法`chain`。`chain`很像`map`，但是它期望转换返回一个`Either`，所以它没有将应用转换的结果包装在一个新的`Either`中。

```
const Left = x => ({
    map: fn => Left(x),
    chain: fn => Left(x),
})

const Right x => ({
    map: fn => Right(fn(x)),
    chain: fn => fn(x),
}) 
```

Enter fullscreen mode Exit fullscreen mode

`Left.chain`仍然没有应用转换，它返回一个包含错误的`Left`,所以我们确定我们不会对已经发生的错误进行操作。

`Right.chain`将把转换`fn`应用到包含的值并返回结果，而不把它包装在另一个`Right`中，因为它期望函数`fn`返回一个`Either`。如果我们在一个真实的项目中实现它，我们可能想要检查`fn`是否返回一个`Either`，如果不返回，就抛出一个错误。

我们可以在前面的例子中使用`chain`来确保我们不会以一个`Either`在另一个`Either`中结束。

```
const result = Right(3)
    .chain(x => x % 2 == 0
        ? Right(x)
        : Left('Odd'))

result // -> Left('Odd') 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只有一个`Left`，如果我们的值是偶数，我们会有一个`Right`。

仅此而已。我们可以使用`map`将转换应用到我们包含的值中，并将其保存在同一个`Either`中，或者如果我们希望应用一个转换，返回另一个`Either`，因为它可能会失败。

尽管能够对一个值进行操作而不关心它是否是错误是很好的，但是如果我们不能访问这个值，它就没有多大用处。现在，该值永远包含在一个`Either`中，我们永远不会知道操作是否成功，转换是否应用到该值，或者我们是否有一个错误等待处理。

我们可以实现最后一个方法来解决这个问题:`fold`。`fold`接受两次回调，如果`Either`包含错误，将调用第一次回调(或*左*，如果`Either`包含有效值，将调用第二次回调(或*右*)。

```
const Left = x => ({
    map: fn => Left(x),
    chain: fn => Left(x),
    fold: (fnLeft, fnRight) => fnLeft(x),
})

const Right x => ({
    map: fn => Right(fn(x)),
    chain: fn => fn(x),
    fold: (fnLeft, fnRight) => fnRight(x),
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有一个`Left`，`fnLeft`将被调用，所以我们可以在那个函数中处理错误。如果我们有一个`Right`，那么`fnRight`将被调用，我们可以用它在 HTTP 响应中发送值，或者把它存储在数据库中，或者用那个值做我们需要的任何事情。

```
Right(3)
    .chain(x => x % 2 == 0
        ? Right(`${x} is even.`)
        : Left('Odd'))
    .fold(
        console.error,
        console.log
    ) 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的例子通过在`console.error`中打印错误来处理错误，并在`console.log`中打印有效值，但是我们可以用我们需要的任何其他方式来处理错误和成功。

# 得心应手要么工厂

有一些我们可以轻松实现的通用工厂。

## 也许吧

Maybe 是一种众所周知的数据结构，在某些语言中称为**可选的**，它可能包含也可能不包含值。我们可以用一个`Either`对它建模，如果它有值，它将是一个`Right`，如果没有值，它将是一个空的`Left`。让我们看看如何建立它。

```
const maybe = value =>
    (value != null
        ? Right(value)
        : Left()) 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果你没那么喜欢三元运算符，

```
const maybe = value => {
    if (value != null) {
        return Right(value)
    }
    return Left()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 试捕

有时，我们可能希望调用一个可以抛出异常的函数，并将该异常视为带有`Either`的错误。如果我们使用`Either`来处理代码中的错误，并且需要与一个通过抛出异常来处理错误的库接口(并期望用户捕获它们)，这可能会很方便。

```
const tryCatch = (fn, ...args) => {
    try {
        const result = fn.apply(null, args)
        return Right(result)
    } catch (e) {
        return Left(e)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 有条件

我们可能希望检查一个值是否满足某个条件，如果不满足，就返回一个错误。我们可以定义一个工厂，该工厂将接受一个谓词(即，一个检查值的条件并返回`true`或`false`的函数)和值，如果给定值的条件成立，则返回`Right`，否则返回`Left`。如果值不满足条件，我们可以使用一个带有错误值的额外参数(通常是一条解释为什么值不被接受的消息)。

```
const condition = (pred, value, reason) =>
    (pred(value)
        ? Right(value)
        : Left(reason)) 
```

Enter fullscreen mode Exit fullscreen mode

还记得我们之前实现的`maybe`工厂吗？原来只是`condition`的一个特例。

```
const maybe = value =>
    condition(x => x != null, value) 
```

Enter fullscreen mode Exit fullscreen mode

# 何时使用任一种

我个人的观点是,`Either`仅仅是一种处理应用程序错误的策略，选择这种或那种策略更多的是个人喜好问题。

一些语言，如 Python 或 Java，提供了一个经过深思熟虑的异常系统，可以用来处理任何可能发生的应用程序错误。在这些语言中，保持事物的习惯性通常是个好主意。

其他语言没有异常系统，并期望程序员在函数调用中出错时返回一个错误值(我看着你呢，开始)。然后我认为使用一个`Either`比返回`(err, result)`更好，每次我们调用函数时都必须检查`err`，特别是如果我们需要将错误向上传递一层，在那里它可以被处理。

然后是 Javascript。它有一个例外系统。算是吧。问题在于，用 Javascript 的异常系统捕捉特定的错误，同时让其他错误传播，这并不是一项简单的任务。因此，使用`Either`处理应用程序错误并为编程错误留下异常可能是值得的，而不是捕捉异常并试图找出它是否是应该在这里或其他地方处理的错误，或者使应用程序崩溃。

就这样了，各位，感谢阅读！