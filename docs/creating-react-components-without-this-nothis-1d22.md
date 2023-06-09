# 创建 React 组件而不使用它。#没有这个

> 原文：<https://dev.to/joelnet/creating-react-components-without-this-nothis-1d22>

[![Change](img/932bedeaa6bcd2240cb2214b4a8db951.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HIZ2ktkZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ad7hzi7gam16no4c19ez.jpg)

轻松创建完全没有`this`的 React 组件。

你可能还记得我这个写了[重新思考 JavaScript:彻底消除和根除 JavaScript 的精神病患者](https://dev.to/joelnet/rethinking-javascript-the-complete-elimination-and-eradication-of-javascripts-this-3m3j)

好了，我回来了，这次我为你准备了一个新的 React 组件！

# 可是为什么，你生气地问？

我赞同道格拉斯·克洛克福特的思维方式。

> “我们知道有几个(特性)会变坏。最糟糕的是阶级。类是 JavaScript 中最受欢迎的新特性。所有的请求都来自 Java 程序员，他们必须用 JavaScript 编程，但不想学习如何去做。所以他们想要看起来像 Java 的东西，这样他们会更舒服。那些人在进入坟墓时并不知道他们有多讨厌。——[道格拉斯·克洛克福特](https://www.youtube.com/watch?v=PSGEjv3Tqo0&t=5m)

正如道格拉斯·克洛克福特发现他的程序在停止使用它后得到了改进一样，我发现在我的代码中也是如此。

> “我非常惊讶地发现我的程序变得更好了。**不用`this`** 也不是什么难事。这实际上是一场义演。我的程序变得越来越小，越来越简单，你知道，这就是我们一直在寻找的东西”——[道格拉斯·克洛克福特](https://www.youtube.com/watch?v=PSGEjv3Tqo0&t=9m)

即便如此，我也明白我改变你想法的可能性很小，因为...

> “程序员和正常人一样情绪化，不理智。**所以当解决方案最终到来时，我们大多数人都会拒绝它**。——[道格拉斯·克洛克福特](https://www.youtube.com/watch?v=PSGEjv3Tqo0&t=13m25s)

这不仅仅是一些轶事。克罗克福德继续提供真实世界的例子，如...

> “一代人都认为后藤是个坏主意。20 年来，我们一直在激烈地争论是否应该使用 GOTO。”——[道格拉斯·克洛克福特](https://www.youtube.com/watch?v=PSGEjv3Tqo0&t=14m5s)

嗯，`this`是 JavaScript 的`GOTO`,我知道它不会在一夜之间消失。但是我希望它不会像`GOTO`那样花费我们 20 年的时间。

[![Doctor is hurts when i .this](img/f78b02c08da400aedd8c61a9c674e814.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FjKloYub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/joelnet/nothis/blob/master/assets/meme-doctor.jpg%3Fraw%3Dtrue)

# 这一切对 React 组件意味着什么？

React 确实有一个功能组件，但 React 最流行的创建组件的方式是创建一个类，并从`React.Component`扩展，一个`Class`随`this`而来。

然后我问自己，如果我可以用`React.Component`但是没有`this`会怎么样。这就是`NoThis.Component`的诞生。

# 

你会注意到`NoThis.Component`的用法和`React.Component`很相似。您之前通过它访问的上下文可以作为第一个函数参数使用。

```
import React from 'react'
import NoThis from 'nothis-react'

class Counter extends NoThis.Component {
  state = { count: 0 }

  increment(ctx) {
    ctx.setState(state => ({ count: state.count + 1 }))
  }

  render(ctx) {
    return (
      <div>
        <button onClick={ctx.increment}>{ctx.state.count}</button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`increment`和`render`变成了纯函数，只作用于它们的输入！

# 变元析构

如果你像我一样喜欢析构，那么像这样的代码现在变得可能了！

```
import React from 'react'
import NoThis from 'nothis-react'

class Counter extends NoThis.Component {
  state = { count: 0 }

  increment({ setState }) {
    setState(({ count }) => ({ count: count + 1 }))
  }

  render({ increment, state: { count } }) {
    return (
      <div>
        <button onClick={increment}>{count}</button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一些性感的代码。

# 排除功能

通过将函数写成类属性，可以将其从`nothis`中排除。

```
class Counter extends NoThis.Component {
  increment = () => {
    this.setState(({ count }) => ({ count: count + 1 }))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

这是一些实验性的未来代码，所以就这样对待它吧。请使用它，并在 [Github repo](https://github.com/joelnet/nothis-react) 上报告任何错误或请求功能。

加入`#nothis`运动，然后[用推特](https://twitter.com/joelnet)联系我。

如果你讨厌这篇文章，你可能也会讨厌这些:

*   [❤·拉姆达-带有特殊占位符的部分应用](https://dev.to/joelnet/i--ramda---partial-application-with-curry-2b02)
*   [函数式编程:IF # Functional # JavaScript #函子的替代方案](https://dev.to/joelnet/functional-programming-how-would-you-if-no-else-javascript-59ai)
*   [让你的代码保持沉默](https://dev.to/joelnet/keep-your-code-dumb-3c)

`#nothis`

[![Cheers!](img/0e0809a227b4e8d55ae5bc09d2845629.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T_va1NDK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/np3h0zqssbzq02869c9u.jpg)