# 通过 React 初学者工具包学习 Apollo

> 原文：<https://dev.to/piglovesyou/learning-apollo-toolset-with-react-starter-kit-fo8>

如今，阿波罗工具集非常吸引人。这是一个基于 GraphQL 规范的相对较大的项目，因为 Apollo 开发团队为用户的增量采用提供了许多独立的模块，而不是所谓的全栈整体框架。好处是巨大的。

然而，因为很难了解 Apollo 的全貌以及每个模块之间的关系，特别是当你使用它进行服务器端渲染(SSR)时，我们必须有一个好的示例项目。在这里，这将是你的选择。

👉[react-starter-kit:feature/Apollo-pure](https://github.com/kriasoft/react-starter-kit/tree/feature/apollo-pure)

它包括:

*   `apollo-client`和`react-apollo`
*   `apollo-link-state`用于客户端状态管理
*   `apollo-server`为图形 api 服务
*   从服务器到客户端传输流畅数据的 SSR

React Starter Kit (RSK)是一个基于 React+Express 的 web 开发 SSR 模板。它不仅是通用 web 服务的高性能模板，也是现代前端开发的具体教材。尤其是 server.js 中的这几行非常漂亮，我已经读过一百遍了。这就像是 web 开发如何发展的结晶。因此，我信任 RSK，并在它身上推行了阿波罗计划。

已经有@langpavel 的 apollo 特色分支，从那里我学到了很多。这两者的主要区别是我的不包含 Redux 代码，被`apollo-link-state`取代。此外，`feature/apollo-pure`使用了`apollo-server`,这样你就可以从阿波罗项目的前端到后端看到一个俯视图。

这次我面临的一个挑战是设计一个舒适/易于理解的`state`角色目录的位置。尽管 Apollo 提供了一种经过深思熟虑的方法，通过 GraphQL 语法透明地访问客户端状态和远程数据，但由于某些原因，它们是不同的。所以我把这些分在所有客户端状态及其解析器都去的`src/data`(通过@langpavel )和`src/state`。

一如既往，感谢您阅读本文，如果您能[回顾该分支机构并就此 PR](https://github.com/kriasoft/react-starter-kit/pull/1666) 发表评论，我将不胜感激。我可能在里面漏掉了阿波罗的一些必要功能。我想让它成为真正的阿波罗样板。请告诉我它是什么。谢谢👍