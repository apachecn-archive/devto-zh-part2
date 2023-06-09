# 异步/等待和绝望的深渊

> 原文：<https://dev.to/burkeholland/asyncawait-and-the-foreach-pit-of-despair-2267>

JavaScript VM 就像一个吃了糖的 4 岁小孩；没穿裤子尖叫着通过你的代码，只是在它用记号笔在你的墙上画画的时候一次执行所有的事情。

Async/Await 就像同一个 4 岁孩子在打盹。你的代码就在那里，就像一个完美的天使，做你想做的事情，你想知道你怎么会如此热爱任何事情。你的内心可能会充满自豪。你可能需要多出去走走，因为我们在这里谈论的是编程。

但是 async/await 并不全是手指画和“幼儿园的第一天”。它有一些奇怪的边缘情况，会让你怀疑自己是否犯了一个巨大的错误，甚至决定使用 JavaScripts。别那样看着我。如果你是家长，你就知道我在说什么。

我在一个大型项目中遇到过这种奇怪的边缘情况，代码的复杂性使得调试变得非常困难。我一开始并不聪明，所以这是一场完美的风暴。

## 异步/等待基础

为了确保我们都使用相同版本的“热面包”，让我们看一个简单的 async/await 示例。

[https://codepen.io/burkeholland/embed/GXOBoG?height=600&default-tab=result&embed-version=2](https://codepen.io/burkeholland/embed/GXOBoG?height=600&default-tab=result&embed-version=2)

Axios 返回一个承诺，但是我们可以等待这个承诺，这样我们就不必处理任何更多的社会可接受的回调。

这很好，但是如果我们想在那个`forEach`循环中进行异步调用呢？很简单，只需将回调标记为`async`，然后继续你的工作。

[https://codepen.io/burkeholland/embed/MqrwWX?height=600&default-tab=result&embed-version=2](https://codepen.io/burkeholland/embed/MqrwWX?height=600&default-tab=result&embed-version=2)

这个管用。或者至少看起来是这样。这里有什么问题？问题是这个循环没有按顺序执行项目。不相信我？看看当我们把指数扔进循环时会发生什么…

[https://codepen.io/burkeholland/embed/pOpJyV?height=600&default-tab=result&embed-version=2](https://codepen.io/burkeholland/embed/pOpJyV?height=600&default-tab=result&embed-version=2)

混乱。事情就是这样。就像一个听说过的 4 岁小孩关于午睡时间的权利。

为什么会这样？forEach 不是应该是同步操作吗？

是的。但是我们传入了一个异步函数，它告诉虚拟机它可以按照自己想要的方式执行这些事情，“按照自己想要的方式”就是“看着世界燃烧”。

这不好。这不是解决任何问题的方法。更糟糕的是，如果你对`forEach`有太多错误的信任，这将难以调试。我应该知道。Async/await 基本上否定了内置数组循环的一个极其重要的方面。

那么我们如何解决这个问题呢？修复方法是进入一个`for of`循环。

[https://codepen.io/burkeholland/embed/pOpJNK?height=600&default-tab=result&embed-version=2](https://codepen.io/burkeholland/embed/pOpJNK?height=600&default-tab=result&embed-version=2)

这就是我们一直想要的。

我不是第一个写这个的人。互联网上充斥着关于 async/await 如何变化的帖子。也有很多人说不要用`forEach`，因为它“更慢”，或者类似的话。我不知道。我实际上并没有读那些文章。

我不会建议你使用或不使用`forEach`，它肯定有它的位置。请记住，当你在一个`forEach`循环中执行 async/await 时，你做错了。不要担心——你会知道你做错了，因为它不能正常工作，JavaScript VM 的那个精神病小孩会把你的生活搞得天翻地覆。

延伸阅读:[从回调到承诺和异步/等待](https://www.zeolearn.com/magazine/javascript-moving-from-callbacks-to-promises-and-async-await)