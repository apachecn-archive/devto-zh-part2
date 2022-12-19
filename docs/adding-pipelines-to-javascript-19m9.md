# 向 JavaScript 添加管道

> 原文：<https://dev.to/bnevilleoneill/adding-pipelines-to-javascript-19m9>

#### 管道实现了操作的清晰链接，但是它们还没有完全准备好

[![](../Images/91f645ccb5e1d7e4a5a3ac1ed2db36b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FBEz3TcN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APetXOaNd__kR0_KEbHuXgA.jpeg)

使用管道来运输水、空气、气体或任何其他流体物质，已经使我们认为理所当然的许多事情成为可能。管道允许我们把水输送到家里，这样我们就可以使用像水槽和浴缸这样的东西。我们的烤箱和加热系统依靠管道输送气体。如果你仔细想想，即使是用杯子喝水的吸管也是一根小管子！

在软件中，管道获取管道左侧存在的任何表达式的输出，并将其用作管道右侧的输入。就像它们在现实世界中的对应物一样，一个管道的输出可以很容易地连接成另一个管道的输入。

### 那么管道是如何工作的呢？

如果没有管道，我们通常会通过将函数参数放在括号内来应用它们——例如，我们可以做`myFunction(argumentToApply)`。管道只是给了我们另一种应用参数的语法。有了管道，我们可以像这样做同样的功能:`argumentToApply |> myFunction`。

如果你没怎么用过管道，你可能会想，“就这样？为什么这甚至值得任何人谈论？”。在某种程度上，添加简单的管道操作符不会改变 JavaScript 的工作方式太多。关于管道令人兴奋的事情是，它们使得某些类型的操作非常容易阅读！

```
const toLower = str => str.toLowerCase();
const prepend = (prefix, str) => prefix + str;
const trim = str => str.trim()

// Standard way.
toLower(prepend("🚧 ", trim(" pipelines! 🚧 . ")))

// With pipeline sugar 🍭
" pipelines! 🚧"
|> trim
|> (str => prepend("🚧 ", str))
|> toLower

// Result either way: "🚧 pipelines! 🚧" 
```

如你所见，任何一种方法都是“正确的”,因为它产生相同的结果。但是使用管道语法可以让我们大大减少嵌套括号的数量。

如果您经常处理数据，您可能会在数据进入应用程序时对其进行大量操作，以便更好地适应应用程序使用数据的方式(我知道我是这样做的！).管道对于描述这些转换集合非常有用，因为不必向后读取数据转换*(从最里面的括号到最外面的调用)，您可以按照它们执行的顺序从上到下读取它们。*

 *像这样使用管道也鼓励我们创建许多小的、孤立的函数，每个函数只做一件事。稍后，当我们需要做更复杂的事情时，我们可以将所有的原子函数以一种清晰可读的格式缝合在一起！这有助于测试我们的应用程序逻辑(我们没有一个*大规模的*函数来做*所有的事情*)以及稍后重用逻辑。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 是什么支撑着管道？

现在 TC39 上有很多令人兴奋的提议，为什么我们要一头扎进管道里呢？首先，管道已经存在于大量的函数式语言中——Elm、Reason、Elixir、甚至 Bash！因为它们已经存在，我们可以很容易地看到它们是如何基于在其他生态系统中的使用来提高代码可读性的。

另一个原因是，管道有可能使嵌套操作*更干净*，类似于箭头函数使匿名函数更容易理解的方式(除了它们增加的微小功能差异之外)。看到他们使用 JavaScript，以及他们可能对 JavaScript 中的函数式编程产生的影响，这让我非常兴奋

作为 ECMAScript 管道的一个很酷的补充，它们还没有准备好在短期内加入。该提案目前处于第一阶段，这意味着关于什么是 JavaScript 管道的讨论仍在如火如荼地进行中(如果你想快速复习一下规范&提案流程，请查看[这份文档](https://tc39.github.io/process-document/)中的一个很棒的小图表)。目前，有三个主要的提案在竞争，详细描述 JavaScript 中管道操作符的样子。

#### 1)无附加条件:简单管道

顾名思义，简单管道提议是管道竞争者中最不复杂的，其行为与我们上面看到的例子完全一样。在这个建议中，管道只负责获取左边计算的表达式，并将其用作右边函数的输入。

```
" string" |> toUpper |> trim 
```

在简单管道中使用箭头函数需要用括号括起来。

```
" string" |> toUpper |> (str => str + " 😎") 
```

另一个“陷阱”是左侧的输出作为一个*单参数*传递给右侧的函数。这意味着，如果我们有一个函数需要两个参数，我们需要把它包装在一个箭头函数中，以保证我们的参数顺序正确。

```
" string" |> toUpper |> (str => prepend("😎 ", str) 
```

正因为如此，使用简单的管道有助于促进 curried 函数的使用——特别是当从左侧传入的数据是函数的最后一个参数时。如果我们处理我们的 prepend 函数，它将更容易添加到我们的管道中，因为它现在不需要包装一个箭头函数。

```
const prepend = prefix => str => prefix + str

" pipeline with currying 🤯"
|> toUpper
|> prepend("🤯 ") 
```

因为我们的 curried prepend 函数返回一个接收 toUpper 输出的新函数，所以它使管道变得相当干净！

关于简单管道提议的最后一个值得注意的事情是，对于 await 没有特殊处理。在这个提议中，甚至不允许在管道中等待。使用它会抛出一个错误！

#### 2)添加异步魔法:F#管道

F#管道提议非常接近简单管道提议。唯一的区别是能够在管道链中使用 await 来支持异步操作。在管道中间使用 await 等待左边的函数解析，然后再在管道上运行这些函数。

```
url
|> fetch
|> await
|> (res => res.json())
|> doSomeJsonOperations 
```

上面的例子应该是这样的:

```
let first = fetch(url)
let second = await first;
let third = second.json()
let fourth = doSomeJsonOperations(third) 
```

#### 3)最后但同样重要的是:智能管道

添加管道的最后一个主要建议来自 Hack，一种源于脸书的 PHP 方言。在 Hack 管道中，管道左侧表达式的输出被放到一个*标记*中，供管道右侧的表达式使用。

在 Hack 中，使用的令牌是$$，但是 ECMAScript 提案一直在考虑使用类似#的东西作为令牌。JavaScript 中的黑客风格管道可能是这样的:

```
"string" |> toUpper(#) |> # + " 😎" 
```

除了需要在管道右侧使用#的“标记样式”之外，智能管道还允许“裸样式”。这种“简单风格”更接近于简单的/F#管道提议。根据所使用的样式，管道会以不同的方式分配管道的左侧值。这就是他们如此“聪明”的原因！

```
// With smart pipes

" string" 
|> toUpper
|> # + " 😎"
|> prepend("😎 ", #)

// Without pipes
prepend(
  "😎 ",
  toUpper(" string") + " 😎"
) 
```

此外，也允许在管道中间使用 await。使用令牌的可能性以及编写异步管道的能力允许这个提议提供一个非常灵活的管道，可以处理几乎任何组的嵌套操作。

然而，与简单管道和 F#提议相比，向智能管道添加额外的糖确实使语法变得相当复杂。除了添加管道语法(`|>`)之外，还需要商定并实现占位符语法。

关于这些智能管道的另一个问题是，关于管道如何工作，有许多语法上的甜言蜜语和“魔法”。将这种魔法添加到管道中可能会导致一些混乱的行为，在某些情况下甚至会对可读性产生负面影响。

当然，由于这个提议仍在充实中，这些问题正在被考虑，我当然希望我们最终能得到一个既简单易懂又使用起来优雅的东西。

### 管道和 JavaScript 生态系统

正如我们所看到的，管道还没有成为 ECMAScript 规范的一部分——它们还处于第一阶段，对于它们应该是什么还有很多不同的意见。

然而，我们不要把这些相互竞争的提议看作是一件坏事！不同意见和建议的数量是 JavaScript 生态系统的重要组成部分。JavaScript 的未来轨迹正在被公开确定——任何人都可以发表自己的想法，并对这门语言产生真正的影响。像你我这样的人可以在 GitHub 上阅读这些关于 [TC39 repo 的讨论，看看有什么东西将被添加到语言中！](https://github.com/tc39/proposal-pipeline-operator)

虽然有些人可能认为 JavaScript 的这种“开源”开发是一个弱点(因为如此多声音的输入可能会降低语言的“凝聚力”)，但我认为这是许多人，包括我自己，喜欢 JavaScript 语言和生态系统的地方。

此外，Babel 目前正在为这 3 个管道提案开发[插件](https://babeljs.io/docs/en/babel-plugin-proposal-pipeline-operator)，这样我们作为开发者就可以在提案被完全采纳之前玩玩它们*。对于 JavaScript，向后兼容性非常重要。我们不希望老网站在添加新的语言功能时崩溃！能够在规范和批准过程中尝试这些语言特性对于 JavaScript 生态系统来说是非常重要的，因为开发人员可以在语言特性确定之前表达任何担忧。*

 *如果你想开始玩 JavaScript 中的管道，看看我作为游乐场制作的这个 repo 。目前，唯一被 Babel 支持的提议是简单的管道提议，所以这是我唯一可以考虑的提议。也就是说，其他两个语法的工作正在进行中，一旦插件支持了其他两个语法，我会尝试更新。

你对管道提案有什么看法？你最喜欢哪个提议？如果你有任何问题，请随时联系我或发微博给我。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * ***