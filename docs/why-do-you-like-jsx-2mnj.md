# 你为什么喜欢 jsx？

> 原文：<https://dev.to/avalander/why-do-you-like-jsx-2mnj>

我使用带有虚拟 DOM 的框架和库已经有一段时间了(特别是， [React](https://github.com/facebook/react) ， [Cyclejs](https://github.com/cyclejs/cyclejs) 和 [Hyperapp](https://github.com/hyperapp/hyperapp) )，许多使用它们的人似乎更喜欢 jsx 而不是类似 hyperscript 的助手。

当我第一次开始使用 React 时，我非常喜欢 jsx，因为它接近 HTML，并且比我所知道的任何模板语言都更强大。对于常规 HTML 语法做不到的事情，您可以去掉几个括号，编写 Javascript 代码。

但是后来我发现了类似 hyperscript 的帮助器，它允许我使用普通的 Javascript 函数创建 vdom 元素，这些函数的名称类似于`div(...)`，我立刻就喜欢上了这种方法。

来对比一些代码:

```
// JSX
<div class="container">
    <h1>Ponies</h1>
    <PonyList>
        { ponies.map(({ name, type }) => <PonyItem name="name" type="type"/>) }
    </PonyList>
</div> 
```

```
// Hyperscript
div({ class: 'container' }, [
    h1('Ponies'),
    PonyList(
        ponies.map(({ name, type }) => PonyItem({ name, type }))
    )
]) 
```

两者都很容易阅读。这一行的第一个单词告诉你你正在创建哪个 HTML 元素或组件。Jsx 看起来更像我们习惯的 HTML。然而，我们想要的动态行为越多，我们需要内嵌的 Javascript 代码就越多。另一方面，对于 hyperscript-helpers，我们不需要任何特殊的语法，一切都只是普通的 Javascript。

根据记录，jsx 仍然胜过必须用`React.createElement`创建元素，但是对于 React 也有 [hyperscript 替代方案](https://github.com/ohanhi/hyperscript-helpers)。

从我的角度来看，这两种方法都足够好了，但是我不明白为什么我更喜欢 jsx 而不是普通的 Javascript，因为它似乎没有提供任何额外的好处。

我的问题是，为什么你更喜欢 jsx 而不是 hyperscript？它提供了我不知道的优势吗？