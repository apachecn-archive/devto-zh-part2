# 使用命名返回变量捕获 Go 中的混乱

> 原文：<https://dev.to/joncalhoun/using-named-return-variables-to-capture-panics-in-go-1l28>

> *这篇文章最初发表在我的网站上[calhoun . io](https://www.calhoun.io/using-named-return-variables-to-capture-panics-in-go/)T3】*

这将是一个简短的帖子，灵感来自肖恩·凯利不久前的推文。

液体错误:内部

我们的目标只是记录和说明一种需要命名返回变量的情况，所以说了之后，让我们直接进入主题。

假设您正在编写一些代码，这些代码使用了一个可能会死机的函数，而出于某种原因(第三方库、向后兼容等)，您无法更改该函数。

```
func pressButton() {
  fmt.Println("I'm Mr. Meeseeks, look at me!!")
  // other stuff then happens, but if Jerry asks to
  // remove 2 strokes from his golf game...
  panic("It's gettin' weird!")
} 
```

Enter fullscreen mode Exit fullscreen mode

您仍然需要使用这个函数，但是如果它死机了，您希望捕获死机并将其作为一个错误返回，所以您编写一些代码如下:

```
func doStuff() error {
  var err error
  // If there is a panic we need to recover in a deferred func
  defer func() {
    if r := recover(); r != nil {
      err = errors.New("the meeseeks went crazy!")
    }
  }()

  pressButton()
  return err
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/wzkjKGqFPL](https://play.golang.org/p/wzkjKGqFPL)T3】*

然后你去运行你的代码...这是什么？你的错误是`nil`即使代码死机？这不是我们想要的！

## 为什么会这样？

虽然一开始看起来我们的代码正在返回我们在函数开始时创建的`var err error`,但事实是我们的程序从未到达那一行代码。这意味着它实际上从不返回那个特定的`err`变量，在我们的延迟函数中修改它最终是没有意义的。

在对`pressButton`的调用之后、返回之前添加一个`Println`确实有助于说明这一点:

```
pressButton()
// Nothing below here gets executed!
fmt.Println("we got here!")
return err 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/Vk0DYs20eB](https://play.golang.org/p/Vk0DYs20eB)T3】*

## 我们如何修复？

要解决这个问题，我们可以简单地使用一个命名的返回变量。

```
func doStuff() (err error) {
    // If there is a panic we need to recover in a deferred func
    defer func() {
        if r := recover(); r != nil {
            err = errors.New("the meeseeks went crazy!")
        }
    }()

    pressButton()
    return err
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/bqGOroPjQJ](https://play.golang.org/p/bqGOroPjQJ)T3】*

产生的代码看起来非常相似，但是通过在声明函数时命名我们的返回变量，我们的程序现在将返回`err`变量，即使我们从未在`doStuff`函数的末尾命中 return 语句。由于这个微小的差异，我们现在可以改变延迟函数中的`err`变量，并成功捕获恐慌。