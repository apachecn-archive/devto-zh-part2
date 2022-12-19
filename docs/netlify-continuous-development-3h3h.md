# 网络持续发展

> 原文：<https://dev.to/8eecf0d2/netlify-continuous-development-3h3h>

几个月前，Netlify 发布了 Netlify Functions，它在 Netlify 连续部署平台中提供了 AWS Lambda 的大部分功能。

当我第一次开始玩函数时，我对它们的简单性印象深刻，并意识到用它我可以轻松地部署几乎任何可以想象的产品。除了函数之外，他们还编写了一个名为 [netlify-lambda](https://github.com/netlify/netlify-lambda) 的简洁工具，它可以帮助您捆绑您的函数进行部署，并且还可以在本地为它们提供开发服务！

在开始使用 netlify-lambda 之后，我几乎立刻就遇到了维护本地、暂存和生产环境之间的一致性的问题。问题不在于 **netlify-lambda** 没有做它所说的事情，而是函数只是我在 netlify 上使用的几个特性之一。

Netlify 支持一系列额外的特性，你可以用一个名为`netlify.toml`的文件进行配置，但是这些特性都没有像 **netlify-lambda** 这样的本地助手。这意味着您需要编写自己的开发工具和服务器来复制它们，这对于一个应用程序来说是很好的，但是一旦您在几个应用程序上工作，就变得相当不愉快了。解决这个问题的显而易见的方法是编写另一个包，比如 **netlify-lambda** 但是对于所有的东西……所以我这么做了！

该软件包被称为 [netlify-local](https://github.com/8eecf0d2/netlify-local) ，可在 NPM 上获得，简单来说，它将读取你的`netlify.toml`配置，并尽最大努力提供你正在使用的选项的 1:1 模拟。它还支持传入 Webpack 配置，以便在发生变化时进行重建。

在所有功能都 1:1 兼容之前，还有相当多的工作要做，但每天都在取得进展，对大多数人来说，它应该是开箱即用的。