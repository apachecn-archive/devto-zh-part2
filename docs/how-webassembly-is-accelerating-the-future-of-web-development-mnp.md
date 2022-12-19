# WebAssembly 如何加速 Web 开发的未来

> 原文：<https://dev.to/kball/how-webassembly-is-accelerating-the-future-of-web-development-mnp>

[![WebAssembly Powering The Web Future](img/0ca4c47203a3706ca800d207a1dd50e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nXY4k1-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zendev.com/assets/img/posts/wasm-web-future/wasm-powering-web-future.png)

“WebAssembly 会杀死 JavaScript 吗？”

自从 WebAssembly (WASM)开始看起来有可能成为现实以来，这个问题就一直挂在许多开发人员的嘴边。虽然许多人猜测 WebAssembly 将意味着 JavaScript 的终结，但参与创建它的人否认了任何这样的意图。

在 webassembly.org 的官方常见问题解答中，这个问题得到了正面的回答，答案是“WebAssembly 是 JavaScript 的补充，而不是替代。”像 T2 Mozilla 公司前首席执行官 Brendon Eich 这样的专家已经预测了 WebAssembly 和 JavaScript 共同发展的未来。

## Web 发展的未来

虽然 WebAssembly 可能不会扼杀 JavaScript，但它肯定会改变前端 web 开发的面貌。现在看到这种变化的所有方式还为时过早，我们已经走得足够远，可以对 web 发展的未来做出一些强有力的预测。

在 WebAssembly 的支持下，web 开发的未来是:

1.  语言多样
2.  极快
3.  平行的

### WebAssembly 支持语言多样性

WebAssembly 将极大地丰富可用于构建前端应用程序和工具的语言，使开发人员能够用他们可能选择的任何语言构建应用程序。

已经有一个令人印象深刻的[实现列表](https://github.com/appcypher/awesome-wasm-langs)，支持 20 多种不同语言的 WASM 编译，包括以下主要语言:

*   [生锈](https://hacks.mozilla.org/2018/04/javascript-to-rust-and-back-again-a-wasm-bindgen-tale/)
*   [C/C++](https://webassembly.org/docs/c-and-c++/)
*   [C#/。网络](https://github.com/aspnet/blazor)
*   [Java](https://github.com/i-net-software/JWebAssembly)
*   [Python](https://github.com/iodide-project/pyodide)
*   [仙丹](https://github.com/jamen/elixir-wasm)
*   [出发](https://github.com/golang/go/issues/18892)

这种语言的多样化将为这些语言的开发者提供大量的 web 框架，允许他们直接用自己选择的语言开发应用程序。一些早期的例子是 Rust 的 [Yew](https://github.com/DenisKolodin/yew) 和 Go 的[huble](https://github.com/go-humble/humble)(huble 目前将 GopherJS 定位为“编译成 javascript ”,但我希望很快会支持 WASM 后端)

### WebAssembly 支持极速 Web 应用

WASM 将允许一些非常惊人的性能提升。我们已经看到 Firefox 展示了与 JavaScript 相反， [WASM 可以像它通过网络一样快地被编译和执行。](https://hacks.mozilla.org/2018/01/making-webassembly-even-faster-firefoxs-new-streaming-and-tiering-compiler/)

这种解析时间的改进解决了现代大量使用 JavaScript 的 web 应用程序的最大瓶颈之一:初始加载时间。在基于浏览器的设计工具 Figma 发布的一组[基准测试中，使用](https://blog.figma.com/webassembly-cut-figmas-load-time-by-3x-76f3f2395164) [asm.js](http://asmjs.org/) 与 WebAssembly 的实现之间的比较显示，WebAssembly 使加载时间提高了**3 倍**。

对于大型文档，这意味着从 10 秒多到浏览器交互少于 5 秒。这是一个巨大的进步。

运行时性能的改善没有那么显著，但仍然很明显。密集图形操作基准测试将 WASM 与 D3 进行比较，结果显示大型项目中有 30%的改进。

即使大量的 web 应用程序仍然使用 JavaScript，使用 WebAssembly 构建的库和框架也可以让开发人员利用这些速度提升，同时继续主要使用 JavaScript 进行开发。

已经有框架对此进行研究的证据，Ember.js [正在研究其 Glimmer VM](https://github.com/glimmerjs/glimmer-vm/pull/752) 的 WASM 实现。

我认为这一趋势将继续加速。想象一下，JavaScript 框架将编译好的“快速启动”启动序列发送到 WASM，然后用 JavaScript 逐步增强并加载基于 JavaScript 的应用程序，让复杂的 web 应用程序像今天简单的静态网站一样快速启动并达到交互性。

### WebAssembly 将启用并行性

不可否认，这一点更具推测性，因为在今天的 WebAssembly 中还没有完全实现。

自从大约在 2005 年开始向多核处理器转变以来，为了实现更高的性能，软件需要并行化的情况越来越多。

在最近一次关于浏览器未来的主题演讲中，Mozilla 的 Lin Clark 强调了浏览器不能“看不见”并行的 web 体验的一部分是实际的应用程序代码。JavaScript 被设计为单线程语言，虽然像 [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) 这样的新工具在 JavaScript 中实现了一定程度的并行性，但它们仍然很难使用，并且只能在相对粗粒度的水平上工作。

Rust 和 Go 等语言从一开始就是为并行而设计的，这使得编写并行应用程序变得容易得多。WebAssembly 的 [threads](https://github.com/WebAssembly/threads/blob/master/proposals/threads/Overview.md) 功能仍然只是一个提议，但一旦它成为现实，我们将很快能够看到 web 应用程序利用细粒度的并行性。

其中大部分将再次出现在框架和库的层面。我不认为一般的应用程序开发人员会在他们的应用程序中使用 WebAssembly 线程，但是仅仅在框架方面就有巨大的潜力。React 的新[纤程架构](https://github.com/acdlite/react-fiber-architecture)已经在考虑将幕后渲染工作分割成可以传递和延迟的块。

想象一下，一个基于 WebAssembly 的纤程将渲染工作分配给所有可用的内核，然后根据需要将它交给 JavaScript 应用程序。

### Web 发展的前景是光明的

我不知道你怎么想，但我对 WebAssembly 带来的可能性感到非常兴奋，即使我从未直接接触过它，并一直用 JavaScript 编码。随着工具的不断进步，更多的浏览器利用了 Mozilla 所展示的性能，框架和库也越来越多地利用了这种可能性，web 开发的未来是非常光明的！

* * *

附:如果你对这类话题感兴趣，你或许应该[在 Twitter 上关注我](https://twitter.com/kbal11)或者加入我的邮件列表。我发出一份名为“[周五前端](https://zendev.com/friday-frontend.html)”的每周简讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T4】