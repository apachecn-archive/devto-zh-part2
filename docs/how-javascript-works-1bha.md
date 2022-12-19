# JavaScript 如何工作

> 原文：<https://dev.to/rhymes/how-javascript-works-1bha>

最近我们谈论了很多关于 JavaScript 的话题。我偶然看到了标题为“JavaScript 如何工作”的文章集。它们写得很好，充满了解释正在发生什么和事情如何工作的图表。

作者亚历山大·兹拉特科夫的前提如下:

> 事实证明，有很多开发人员每天都在使用 JavaScript，但是他们并不知道幕后发生了什么。

以下是各种文章:

*   [引擎、运行时和调用栈的概述](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)

*   [V8 引擎内部+如何编写优化代码的 5 个技巧](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e):讲述 V8、其编译和优化步骤

*   [内存管理+如何处理 4 种常见的内存泄漏](https://blog.sessionstack.com/how-javascript-works-memory-management-how-to-handle-4-common-memory-leaks-3f28b94cfbec):深入探讨内存管理和垃圾收集(引用计数和标记与清除)的主题。很多高级语言都有类似的垃圾收集机制。

*   [事件循环和异步编程的兴起+用 async/await 更好地编码的 5 种方法](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5):谈论事件循环，解释为什么计时器不能精确，并概述承诺和 async/await。

*   [用 SSE +如何选择正确的路径深入探讨 WebSockets 和 HTTP/2](https://blog.sessionstack.com/how-javascript-works-deep-dive-into-websockets-and-http-2-with-sse-how-to-pick-the-right-path-584e6b8e3bf7):这更多的是关于 HTTP/2、Websockets 和服务器端事件以及每种技术的优缺点。

*   [与 WebAssembly 的比较+为什么在某些情况下使用它比使用 JavaScript 更好](https://blog.sessionstack.com/how-javascript-works-a-comparison-with-webassembly-why-in-certain-cases-its-better-to-use-it-d80945172d79) : WebAssembly 目前对我来说似乎有点粗糙，但如果所有东西最终都要用 JS 编写，它最好有一个 WebAssembly 核心:P 这个用虚幻引擎制作的演示非常棒:[https://S3 . amazonaws . com/Mozilla-games/tmp/2017-02-21-sun temple/sun temple . html](https://s3.amazonaws.com/mozilla-games/tmp/2017-02-21-SunTemple/SunTemple.html)-我不确定我们是否

*   [Web Workers 的构建模块+你应该使用它们的 5 种情况](https://blog.sessionstack.com/how-javascript-works-the-building-blocks-of-web-workers-5-cases-when-you-should-use-them-a547c0757f6a):它们做什么，你如何利用它们，它们的局限性等等。你的应用中有“普通”网络工作者的用例吗？我是说除了在 PWAs 的服务人员。

*   [服务人员、他们的生命周期和使用案例](https://blog.sessionstack.com/how-javascript-works-service-workers-their-life-cycle-and-use-cases-52b19ad98b58):这都是专门针对服务人员和 pwa 的。有一天我也会去 PWAs，我还在“SPA are wow”阶段:D)。我希望在 2018-2019 年，每个浏览器对 PWA 的支持达到 100%，这样我们就可以有一个替代原生应用及其分发方法的严肃选择。IIRC 第一代 iPhone 有一种 PWAs，本地应用是后来才出现的。离线缓存和 HTTP/2 也有助于提高手机的性能。

*   [Web 推送通知的机制](https://blog.sessionstack.com/how-javascript-works-the-mechanics-of-web-push-notifications-290176c5c55d):解释推送 API 和通知 API 以及它们通过服务人员的使用。

*   使用 MutationObserver 跟踪 DOM 中的变化:肯定不知道这个细节。网络已经向前迈进了一大步！有了这个，你可以跟踪用户在你的页面上做的每一件事，令人毛骨悚然的:D

*   [渲染引擎及优化其性能的技巧](https://blog.sessionstack.com/how-javascript-works-the-rendering-engine-and-tips-to-optimize-its-performance-7b95553baeda):讲述 DOM 和 CSSOM 及浏览器渲染引擎，以及如何为该引擎优化 JS 和 CSS。

*   [网络层内部+如何优化其性能和安全性](https://blog.sessionstack.com/how-javascript-works-inside-the-networking-layer-how-to-optimize-its-performance-and-security-f71b7414d34c):浏览器中的联网实际上是如何工作的？这篇文章就是关于这个的。

*   [CSS 和 JS 动画的引擎盖下+如何优化其性能](https://blog.sessionstack.com/how-javascript-works-under-the-hood-of-css-and-js-animations-how-to-optimize-their-performance-db0e79586216):CSS 和 JS 动画如何工作，如何正确使用。我真的在这方面落后了，我不认为我曾经在网络应用程序中使用过动画。

*   [解析，抽象语法树(ASTs) +如何最小化解析时间的 5 个技巧](https://blog.sessionstack.com/how-javascript-works-parsing-abstract-syntax-trees-asts-5-tips-on-how-to-minimize-parse-time-abfcf7e8a0c8):JS 如何转换成 AST，各种引擎如何优化字节码加载。

*   [Babel 和 TypeScript 中的类和继承+trans filling 的内部原理](https://blog.sessionstack.com/how-javascript-works-the-internals-of-classes-and-inheritance-transpiling-in-babel-and-113612cdc220):原型链如何工作，类如何实现和 transpiled。

*   [存储引擎+如何选择合适的存储 API](https://blog.sessionstack.com/how-javascript-works-storage-engines-how-to-choose-the-proper-storage-api-da50879ef576) :讲述各种浏览器存储选项(从 cookies 到 IndexedDB)及其优缺点。

*   [影子 dom 的内部+如何构建自包含组件](https://blog.sessionstack.com/how-javascript-works-the-internals-of-shadow-dom-how-to-build-self-contained-components-244331c4de6e):关于影子 DOM、组件、插槽和作用域 CSS 的全部内容。

*   [WebRTC 和对等网络的机制](https://blog.sessionstack.com/how-javascript-works-webrtc-and-the-mechanics-of-peer-to-peer-connectivity-87cc56c1d0ab) : WebRTC，它隐藏的复杂性和它的用例。

*   [自定义元素+构建可重用组件的最佳实践](https://blog.sessionstack.com/how-javascript-works-under-the-hood-of-custom-elements-best-practices-on-building-reusable-e118e888de0c):如何在现代 JavaScript 中正确使用自定义元素，以及它们有哪些浏览器支持。

*ps。我与公司或博客都没有关系，我只是觉得这个内容很酷:-)*