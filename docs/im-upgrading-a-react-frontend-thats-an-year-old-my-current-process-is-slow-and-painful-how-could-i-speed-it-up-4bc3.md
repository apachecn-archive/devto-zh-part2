# 我正在升级一个一年前的 React 前端。我现在的过程又慢又痛苦。我怎样才能加快速度呢？

> 原文：<https://dev.to/itsarnavb/im-upgrading-a-react-frontend-thats-an-year-old-my-current-process-is-slow-and-painful-how-could-i-speed-it-up-4bc3>

我正在做一个 React 15 项目，它可以在 Node 6 中编译，我正在更新它以适应 React 16/Node 10。

我目前的过程:我使用`npm outdated`来寻找旧的包，并将它们更新到新的版本。之后我运行`npm install`，然后用 webpack 加载前端。

我发现有几个包可能是高度相互依赖的，更新一个包而不进行修改或更新其他包会破坏前端，并在“linkClass.js”中出现一个错误

我将感激任何帮助。