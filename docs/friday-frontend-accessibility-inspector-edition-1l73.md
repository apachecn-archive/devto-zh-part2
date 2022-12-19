# 星期五前端:可访问性检查器版

> 原文：<https://dev.to/kball/friday-frontend-accessibility-inspector-edition-1l73>

这个星期让我非常兴奋和受启发的是新的 Firefox 可访问性检查器。更好的工具是取得更好结果的关键一步，所以我对这将为网络的可访问性带来的改进感到兴奋！

[https://www.youtube.com/embed/c_KUJgkOMrQ](https://www.youtube.com/embed/c_KUJgkOMrQ)

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [IE 中的 CSS 网格:揭穿常见的 IE 网格误区](https://css-tricks.com/css-grid-in-ie-debunking-common-ie-grid-misconceptions/)

信不信由你，你可以在 IE11 中使用 CSS Grid——事实上，IE 是第一批支持 CSS Grid 版本的浏览器之一，因为微软推出了该规范。然而，因为它太早了，而且不再更新了，所以 IE11 版本的 Grid 有一些奇怪的地方。这篇文章详细介绍了它们，并向您展示了如何编写适用于现代浏览器和 IE11 的网格布局，因此，如果支持 IE11 阻碍了您尝试网格，这是为您准备的。

##### [CSS 网格第二级:子网格来了](https://www.smashingmagazine.com/2018/07/css-grid-2/)

一个关于 CSS 网格中子网格的优秀解释者，这是 CSS 网格规范第二轮中将包含的最大/最重要的特性。人们对子网格进入第一轮抱有很大希望，但它们最终被放弃了，以便有更多的时间来弄清楚它们应该如何工作，而不会阻碍其余的工作。工作正在进行中，希望我们将很快看到第一个实现——阅读这篇文章，看看宣传是什么。

##### [如何用 CSS 网格和 CSS 变量快速原型化应用](https://medium.freecodecamp.org/how-to-quickly-prototype-apps-with-css-grid-and-css-variables-8d3d96d68eaa)

没有什么惊天动地的事情，但是很好的演示了用 Grid 构建一个简单的布局并用 CSS 变量对其进行主题化。如果你是那种喜欢边做边学的人，这是一个很好的选择。

##### [明天的布局](https://mxb.at/blog/layouts-of-tomorrow/)

探索 CSS 网格和其他高级 CSS 特性所释放的一些潜力。经过一段时间的最佳实践和相对停滞的布局技术的整合，导致大多数网站看起来都一样，我们现在正在进入一个难以置信的多样化时期。

##### [CSS4 在哪里？它什么时候出来？](https://www.youtube.com/watch?v=Jtmkk6odggs)

谈论 CSS 规范的历史和现在正在发生的事情的短视频。很好地概述了规范过程随时间演变的方式。跨多个规范的分散无疑会导致更多的事情需要跟踪，但我认为这是我们今天看到 web 平台更快速发展的一个重要原因。

#### JavaScript

##### [学习这些 JavaScript 基础知识，成为更好的开发者](https://medium.freecodecamp.org/learn-these-javascript-fundamentals-and-become-a-better-developer-2a031a0dc9cf)

我非常相信基础知识，这篇文章很好地介绍了 JavaScript 语言的许多核心基础知识。有些部分我认为他们可以解释得更好或更详细，但总的来说，这是一篇扎实的文章，介绍了该语言的许多独特特性。

##### [face-api.js —用 tensorflow.js 在浏览器中进行人脸识别的 JavaScript API](https://itnext.io/face-api-js-javascript-api-for-face-recognition-in-the-browser-with-tensorflow-js-bcc2a6c4cf07)

我们继续看到越来越多的机器学习应用程序和工具在 tensorflow.js 的基础上用 JavaScript 开发。这是另一个开源库，让你直接在浏览器中运行整个机器学习管道来进行面部识别，并使用 GPU 加速。我们在未来离开。

##### [Fullstack React 关于在 React 组件中使用引用的指南](https://www.fullstackreact.com/articles/using-refs-in-react/)

Refs 是 React 和 Vue 中真正有趣的特性之一，用于“揭开盖子”和访问底层浏览器 DOM。对于插入第三方库和处理那些虚拟 DOM 抽象有点漏洞的罕见情况非常有用。这本关于在 React 中使用 refs 的完整指南是一流的。

##### [Vue 店面 1.0](https://medium.com/@piotrkarwatka/vue-storefront-1-0-1e6c9cfe8075)

一个用于电子商务应用程序的独立 PWA 店面，您可以将其插入到任何电子商务解决方案的前面，以获得超快速、可扩展和移动优先的前端。该系统包括后端和前端之间的缓存、离线功能等。目前 magento 似乎拥有最多的支持，但适配器系统的设置允许轻松添加额外的电子商务后端。我最近没有涉足电子商务，但下一次我有理由这么做时，我肯定会关注这个领域。

##### [用 Javascript 和 HTML 构建 AR/VR](https://hackernoon.com/building-ar-vr-with-javascript-and-html-28acd1da0371)

自从[基兰·法尔](https://twitter.com/kfarr)向我指出渐进式增强和深度链接的结合使基于网络的虚拟现实比原生虚拟现实更加强大以来，我对网络上的 AR/VR 的潜力感兴趣已经有一段时间了。不过，进入那个世界可能会感觉有点吓人，这正是本文旨在通过给你一个术语定义和在 WebVR 世界中从局外人到局内人的资源列表来解决的问题。

#### 其他牛气

##### [火狐 61——至日量子](https://hacks.mozilla.org/2018/06/firefox-61-quantum-of-solstice/)

最新 Firefox 版本中的两个超级酷的东西。首先，他们现在已经启用了并行 CSS 解析，继续他们的道路，并行化浏览器中的所有东西，以利用多核进程。但是促使我把它包含在时事通讯中的真正有趣的事情是他们增加了一个全新的可访问性检查器，使得调试屏幕阅读器如何与你的网站交互变得容易。这对于帮助开发者使网站更容易访问来说意义重大。

##### [在 Firefox 开发者工具中引入辅助功能检查器](https://www.marcozehe.de/2018/04/11/introducing-the-accessibility-inspector-in-the-firefox-developer-tools/)

这个帖子有点老了，但是受到最近发布的让每个人都可以使用可访问性检查器的启发，我想强调一下。像屏幕阅读器一样与你的站点交互，深入到底层的可访问性树中去理解它是如何工作的以及为什么会出问题。对于可访问性来说，这是一个显著的进步。

##### [行业 JavaScript 使用情况](https://blog.npmjs.org/post/175311966445/javascript-usage-by-industry)

通过对 npm 用户的大规模调查数据进行分解，我们可以看到 JavaScript 的使用是如何因行业而异的。有趣的是，仍然有超过 50%的行业使用 jQuery！我还好奇的注意到有多少人在使用电子产品——每个行业都超过 20%!即使考虑到这可能是更多关注后端/节点的人的受众，这也比我预期的要高。

##### [哎哟，你的 JavaScript 疼！](https://speedcurve.com/blog/your-javascript-hurts/)

看看在考察 JavaScript 性能时，什么样的度量是有意义的，以及一些有助于深入研究的工具。在我们使用的客户端 JavaScript 数量不断增加的情况下，CPU 使用率是一个经常被忽视的成本，但特别是当我们希望扩大美国和欧洲以外的受众时，人们使用的大量设备都是低端智能手机，在 CPU 方面非常有限。

##### [快速调用 WebAssembly 并实现 anyref](https://blog.benj.me/2018/07/04/mozilla-2018-faster-calls-and-anyref/)

上周我在[谈论 WebAssembly](https://changelog.com/jsparty/32) 的时候提到的一件事是，当前的限制之一是 WebAssembly 和 JavaScript 之间的调用非常慢，这往往会减少 WebAssembly 的有用范围。Welp，刚一讨论就提到了这一点，强调了在 Firefox 中进出 WASM 的函数调用速度提高了大约 10 倍的工作。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】