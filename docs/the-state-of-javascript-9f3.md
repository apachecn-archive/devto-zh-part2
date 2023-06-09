# JavaScript 的现状

> 原文：<https://dev.to/addyosmani/the-state-of-javascript-9f3>

我最近在网络秀的状态上谈到了 JavaScript 的[状态。是的，我是只会一招的小马...😃](https://youtu.be/i5R7giitymk)

[https://www.youtube.com/embed/i5R7giitymk](https://www.youtube.com/embed/i5R7giitymk)

在客户端，当你为不容易看到的东西付费时，理解 JavaScript 的成本如何影响你的网站变得很重要。

我如何解决这些问题？

*   [从灯塔开始](https://dev.to/addyosmani/shine-a-light-on-javascript-performance-with-lighthouse-1opf)💡🏠
*   检查你的网站在一些真实手机上的表现。在[网页测试](https://webpagetest.org)上有很多这样的设置。如果您的团队负担得起，本地设备实验室也总是有用的📱
*   如果 JavaScript 影响了您的度量标准，可能是时候分析一下原因了。我经常使用 [source-map-explorer](https://www.npmjs.com/package/source-map-explorer) 、 [webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer) 和 [bundlephobia](https://bundlephobia.com) 来发现削减我的包的机会。
*   我使用 DevTools 代码——覆盖率突出了捆绑包的减肥机会。
*   尝试利用代码分割模式。获得大量供应商捆绑包？最初的页面加载可能只需要其中的几个依赖项。大型应用捆绑包？现在真的需要所有这些逻辑吗？像 React 这样的库开始更好地支持开箱即用的代码分割(参见 [React.lazy()](https://reactjs.org/blog/2018/10/23/react-v-16-6.html#reactlazy-code-splitting-with-suspense) )。
*   考虑使用 [babel-preset-env](https://babeljs.io/docs/en/babel-preset-env) 为现代和传统浏览器生成包。[模块/nomodule 模式](https://philipwalton.com/articles/deploying-es2015-code-in-production-today/)允许您以向后兼容的方式提供这些服务。
*   利用压缩技术。我一直建议团队尝试静态资产压缩。在我自己的应用程序中，我看到 JS 的包大小减少了 20-30%。如果你是一个节点商店，[收缩射线](https://github.com/aickin/shrink-ray)或[收缩射线电流](https://www.npmjs.com/package/shrink-ray-current)是为 Brotli 服务的可靠的中间件，我已经找到可靠的工作。