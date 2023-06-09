# 编译到 JS 动物园

> 原文：<https://dev.to/deciduously/the-compile-to-js-zoo-3n90>

有很多工具承诺让你不用动一点 JavaScript 就能编写你的客户端代码。

开发到社区使用哪一个？为什么我们要放下一切，奔向你选择的工具？仅仅使用 ECMAscript 我们能获得什么？

我个人的第一选择是[重组](https://github.com/Day8/re-frame)。最低限度(750 SLOC！)它强加的结构非常有意义，并且它让您避免构建自己的架构。用 Clojure vectors via Reagent 定义 UI 再自然不过了，而且 interop 是如此无缝，你几乎不会注意到你正在做它。一旦您了解了所有东西的去向，您就拥有了构建高性能、可伸缩的真实应用程序所需的一切。

在我的清单上，下一个要尝试的是[卤素](https://github.com/slamdata/purescript-halogen)，但是除了一个“hello world”应用程序之外，我还没有完全理解它。不过，PureScript 是一项迷人的技术，我很乐意花更多的时间来学习如何使用它。

到目前为止，我用过的最简单的方法是 [ReasonReact](https://reasonml.github.io/reason-react/) 。如果您已经使用了 React，那么您已经可以使用这个工具并获得底层 OCaml 编译器的好处来检查您的代码。

还有几十个我没有提到——那是你的工作！卖给我一些漂亮的东西。

我也很想听听为什么这都是废话，我们应该坚持做已经摆在我们面前的事情。ES6/7/next/infinite 本身就是一个富有表现力的、健壮的工具，在许多情况下直接从这些工具中汲取灵感——下面是您的案例。