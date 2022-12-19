# 什么是箭头函数？

> 原文：<https://dev.to/flippedcoding/what-are-arrow-functions-1e6>

在 web 开发中有很多方法可以完成工作。你有函数，回调函数，方法，箭头函数，以及所有其他的函数。这个小解释的重点是箭头函数，因为它们相对较新。

当 EcmaScript6 (ES6)发布时，箭头函数就成为了一种东西。除了一些事情之外，它们与常规函数非常相似。首先，编写箭头函数的语法比常规函数要简洁得多。我的意思是:

*常规功能*

```
function getPrice(cost, quantity) {
    return cost * quantity;
} 
```

Enter fullscreen mode Exit fullscreen mode

*箭头功能*

```
(cost, quantity) => cost * quantity; 
```

Enter fullscreen mode Exit fullscreen mode

有了箭头函数，你不必写那么多的代码，也能得到同样的结果。当然不仅仅是这样。一件重要的事情是，箭头函数不绑定到关键字 *this* ，这不一定是一件坏事。你实际上可以使用 *this* ，它被绑定到包含箭头函数的代码中，这非常好。

箭头功能也非常灵活。您可以带参数或不带参数使用它们。所以如果你只需要一个参数，你可以使用箭头函数:

```
(day) => 5;
day => 5; 
```

Enter fullscreen mode Exit fullscreen mode

这种情况下的括号是可选的。或者如果你不需要任何参数，你可以使用箭头功能:

```
() => "Candy";
_ => "Candy"; 
```

Enter fullscreen mode Exit fullscreen mode

另一件要记住的事情是，你仍然必须遵循函数的代码块规则。如果你需要函数执行多条语句，你仍然需要像这样使用花括号:

```
() => {
    let x = 7;
    let y = -23;
    return x * y;
} 
```

Enter fullscreen mode Exit fullscreen mode

我需要提醒你使用箭头函数。因为它们是匿名函数(它们没有函数名)，所以调试代码会更加困难。如果您有一堆箭头函数，那么跟踪调用堆栈可能无法找到问题的根源。

你想用这些的主要时候是不想绑定到*这个*的时候。如果你知道你需要在不同的上下文中使用*这个*，箭头函数是最好用的东西。

这是我对箭头函数的快速分解。希望对你有帮助！如果你有任何补充或者问题，请在评论中告诉我。

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)