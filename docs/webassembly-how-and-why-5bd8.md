# WebAssembly:如何以及为什么

> 原文：<https://dev.to/bnevilleoneill/webassembly-how-and-why-5bd8>

**作者:[米莉卡](https://blog.logrocket.com/author/milicamihajlija/)米哈** ✏️

#### 如何在浏览器中运行本地代码，为什么要这么做，这对 JavaScript 和 web 开发的未来意味着什么

在每一个浏览器中，无论你使用的是 Chrome、Firefox、Edge 还是 Safari，代码都是由 JavaScript 引擎解释和执行的——它只运行 JavaScript。不幸的是，JavaScript 并不适合我们想要执行的每一项任务。这就是 WebAssembly 介入的地方。

WebAssembly 是一种可以在现代浏览器中运行的新型代码。它是为了在网络上获得更好的性能而创建的。它是一种低级的二进制格式，大小很小，因此加载和执行速度很快。你不写 WebAssembly，你编译其他更高级的语言给它。

汇编通常是指类似于机器代码的人类可读语言。机器码是你的处理器理解的，一串数字。

<figure>[![](img/20de7cc4285671ef814588893f7f4e74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mN4-L7gC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AY0nZhsD-0ac5gKHc_OG3TQ.png) 

<figcaption>汇编语言和机器码</figcaption>

</figure>

每一种高级编程语言都被翻译成机器代码，以便在处理器上运行。不同种类的处理器架构需要不同的机器码和不同种类的汇编。

<figure>[![](img/93d3fd3361a044984d751fa4984fa536.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UnQiGqhf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfY3Nj1rqpy1sveLt_Np9jg.png) 

<figcaption>为不同处理器架构编译源代码</figcaption>

</figure>

尽管名为 WebAssembly，但它并不完全是一种汇编语言，因为它并不针对任何特定的机器。它是为浏览器准备的，当你交付要在浏览器中执行的代码时，你不知道你的代码会在什么样的机器上运行。

<figure>[![](img/7c660229d02e96ed1a18c89391083e4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--65JGsu_d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApdkebdFYtLbrvjL88dQ-BA.png) 

<figcaption>WebAssembly 作为中间编译目标</figcaption>

</figure>

WebAssembly 是一种用于概念机器的语言，它是流行的现实世界硬件的最小公分母。当浏览器下载 WebAssembly 代码时，它可以快速将其转换为任何机器的程序集。

这就是 WebAssembly 的样子——它有一个易于阅读的文本格式。wat) ，但是二进制表示是你实际交付给浏览器的*(。wasm)* 。

<figure>[![](img/c5a8460e71b6845217629485d0e9f91a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V2mrndAO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcuLEJje56uGtxt37cUGIWw.png) 

<figcaption>WebAssembly 文本和二进制格式</figcaption>

</figure>

WebAssembly 能让你做的是将类似于 **C、C++或 Rust** 代码的东西编译成所谓的 **WebAssembly 模块**。您可以将它加载到您的 web 应用程序中，并从 JavaScript 调用它。

它不是 JavaScript 的替代品，它与 JavaScript 并行工作。

<figure>[![](img/47bd85d5b2a32ff86d48d1e603522cf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WUyJl-LQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzaeiNOe-kSznuKi1ndPbpw.png) 

<figcaption>应用程序中的 WebAssembly 模块</figcaption>

</figure>

### 我们为什么需要 WebAssembly

想想你需要在浏览器之外使用软件的情况:**视频游戏、视频编辑、3D 渲染或者音乐制作**。这些应用程序需要进行大量的计算，并且需要很高的性能。这种性能很难从 JavaScript 中获得。

JavaScript 最初是一种简单的脚本语言，旨在为充斥着轻量级超文本文档的网络带来一些交互性。它被设计成易于学习和书写，但它没有被设计成很快。多年来，浏览器在它们[解释 JavaScript](https://hacks.mozilla.org/2017/02/a-crash-course-in-just-in-time-jit-compilers/) 的方式上增加了优化，带来了重大的性能改进。

随着速度越来越快，你可以在浏览器中做的事情越来越多。新的 API 带来了交互式图形、视频流、离线浏览等等。反过来，越来越多的丰富的应用程序开始出现在网络上，而这些应用程序以前只在本地使用。今天，你可以通过浏览器轻松地编辑文档和发送电子邮件，但是在某些领域，JavaScript 的性能仍然是一个难题。

视频游戏尤其具有挑战性，因为它们不仅要协调音频和视频，而且往往还要协调物理和人工智能。能够达到在网络上高效运行游戏的性能将为[带来许多其他网络应用](https://webassembly.org/docs/use-cases/)打开大门，这就是 WebAssembly 要做的事情。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 为什么网络如此有吸引力

网络的美妙之处在于它就像✨magic ✨—一样，可以在任何地方工作。没有下载、安装和安全风险，就像本地应用一样。只需一次点击，web 应用程序就会在您需要时立即交付。浏览器已经建立了安全属性，防止在其中运行的代码干扰您的系统。在网上分享非常简单——链接只是可以点击的字符串，你可以把它放在任何地方。

它是唯一真正通用的平台，使您的应用程序可以在任何设备上访问。这也允许您维护一个单一的代码库，使更新简单，并确保每个用户都可以访问您的应用程序。

由于这些内在的力量和网络提供的交互性，我们从超文本和一种小型脚本语言一直发展到一个令人难以置信的强大和流行的平台，其中充满了惊人的应用程序和功能。但是直到现在，它仍然是由相同的脚本语言驱动的，而这种语言从来没有真正被设计来做所有这些事情。

### web assembly 带来了什么

以下是 WebAssembly 的特别之处，它非常适合 web:

*   **速度**
*   **便携性**
*   **灵活性**

WebAssembly 是为**速度**设计的。它的二进制文件比文本 JavaScript 文件小得多。由于它们的大小，下载速度更快，这在慢速网络上尤其重要。

它们的解码和执行速度也更快。JavaScript 是一种动态类型语言，变量类型不需要预先定义，也不需要预先编译。这使得编写变得简单快捷，但也意味着 JavaScript 引擎有更多的工作要做。当代码在页面上执行时，它必须解析、编译和优化代码。

解析 JavaScript 包括将纯文本转换成一种叫做[抽象语法树](https://en.wikipedia.org/wiki/Abstract_syntax_tree) (AST)的数据结构，并将其转换成二进制格式。WebAssembly 以二进制形式交付，解码速度更快。它是静态类型的，所以与 JavaScript 不同，引擎不需要在编译期间推测将使用什么类型。大多数优化发生在源代码编译期间，甚至在它进入浏览器之前。内存是手动管理的，就像 C 和 C++这样的语言一样，所以也没有垃圾收集。所有这些都提供了更好、更可靠的性能。**WASM 二进制代码的执行时间只比相同的本地代码慢 20%。**

<figure>[![](img/ed891bfaccb8ebaf4a5b2331004ec94d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xxtILaoB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AxU7akQpF9KctXbQA.png) 

<figcaption>在 JavaScript 引擎中处理 WebAssembly 花费的相对时间</figcaption>

</figure>

设计 WebAssembly 的主要目标之一是**可移植性**。要在设备上运行应用程序，它必须与设备的处理器架构和操作系统兼容。这意味着为您想要支持的操作系统和 CPU 架构的每种组合编译源代码。有了 WebAssembly，只需一个编译步骤，你的应用就可以在所有现代浏览器中运行。

<figure>[![](img/580ac3dad88bd4ed4a02fdb95bb376b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4h8HxP5D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANT8Fw1gCXa0eYBuCU-MiJA.png) 

<figcaption>编译本机代码在不同平台上运行与编译到 WebAssembly</figcaption>

</figure>

您不仅可以将自己的应用程序移植到 web 上，还可以移植大量的 C++库和开源应用程序。这是一种几乎所有平台都支持的语言，包括 iOS 和 Android。通过 WebAssembly，它可以作为 web 和移动部署的通用语言。

WebAssembly 最令人兴奋的地方是它为 web 编写带来了更多的灵活性。到目前为止，JavaScript 是 web 浏览器中唯一完全受支持的语言。有了 WebAssembly，web 开发人员将能够选择其他语言，更多的开发人员将能够为 web 编写代码。JavaScript 仍将是大多数用例的最佳选择，但现在，当你确实需要一个提升时，偶尔会有一个下降到专门语言的选项。像 UI 和应用程序逻辑这样的部分可以在 JavaScript 中，而核心功能在 WebAssembly 中。当优化现有 JS 应用程序的性能时，可以用更适合问题的语言重写瓶颈。

目前完全支持的语言是 C、C++和 Rust，但还有许多其他语言正在开发中，包括 Kotlin 和。NET，这两者都已经提供了实验性的支持。

### 工作原理

您需要一个工具将您的源代码编译成 WebAssembly。一种方法是使用经验丰富的模块化编译器工具链 [LLVM](https://en.wikipedia.org/wiki/LLVM) ，它可以被设置为与不同的语言一起工作。为了编译 C 和 C++，你可以使用一个更简单的基于 LLVM 的工具，叫做 [Emscripten](http://kripken.github.io/emscripten-site/) 。Rust Nightly 有自己的编译器 [rustc](https://www.hellorust.com/setup/wasm-target/) 可以直接输出 WebAssembly。

如果您有一个用 C 编写的“Hello world ”,这个 Emscripten 命令将生成在浏览器中运行它所需的文件。您得到的是一个 WebAssembly 模块以及 **HTML** 和* *JS ** 文件。

```
emcc hello.c -s WASM=1 -o hello.html 
```

<figure>[![](img/cdcbfeb7dcbe4ce93f2c61779d46ae21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s9d2TJFX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AxNUJSNFWssBQcekQ.png) 

<figcaption>用 Emscripten</figcaption>

</figure>

编译 C/C++代码到 WebAssembly

你需要 HTML 和 JS 文件，因为 WebAssembly 不能直接访问任何平台 API——DOM、WebGL、WebAudio 等。要使用其中的任何一个，甚至要在页面上显示 WebAssembly 代码的输出，您都必须使用 JavaScript。Emscripten 创建 JS 代码，[设置您的模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/instantiate)，并使与 Web APIs 的通信成为可能。HTML 文件加载该 JS 并在 textarea 或 canvas 元素中显示 WebAssembly 输出。

您可以将 WebAssembly 二进制文件视为常规的应用程序模块:浏览器可以获取、加载和执行它们。它们有导入和导出，允许您像处理 JavaScript 对象一样处理它们。您可以在 JavaScript 代码中调用 WebAssembly 函数，也可以在 WebAssembly 模块中调用 JavaScript 函数。

它只有四种基本类型，并且都是数字——整数和浮点数(i32、i64、f32 和 f64)。这意味着在 JavaScript 和 WebAssembly 之间传递更复杂的数据类型并不简单。例如，如果你想传递一个字符串，你必须把它编码成一个数字数组，然后传递一个指向它的指针。它只能从自己的线性内存中读取和写入，并且不能直接访问外部 JavaScript 变量，除非它们被复制到内存中或通过调用堆栈传递。

目前，通过 JavaScript 进行大量调用并不是很快，因为引擎每次都必须做一些设置工作。这在将来可能会改变，但是现在好的建议是把 WebAssembly 看作一个独立运行的系统，用它来卸载大量的工作。

如果你想在没有任何设置的情况下试用它，请访问 [webassembly.studio](http://webassembly.studio) 或 [WebAssembly Explorer。](https://mbebenita.github.io/WasmExplorer/)

### 可以用吗？

**是的！**

它在这里，它是真实的。WebAssembly 支持去年在所有主流浏览器中推出。目前全球用户的 **74.93%** ，甚至桌面用户的 **82.92%** 都支持。作为旧浏览器的后备，你可以使用 Emscripten 编译成[ASM . js](http://asmjs.org/faq.html)——只使用数字(没有字符串、对象等)的 JavaScript 子集。).这是一种直接导致创建 WebAssembly 的格式，它在网络上被广泛使用，例如在上传照片到脸书时用于图像压缩，以及在 Adobe 的 [Lightroom](https://lightroom.adobe.com/) 中用于图像编辑。

<figure>[![](img/16b2d6982a41b811299876a9469ae578.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4s-Q6rJ3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2APliXmTDmymYjfm2G.png) 

<figcaption>支持 web 汇编的浏览器</figcaption>

</figure>

现实世界中已经有一些非常令人兴奋的 WebAssembly 的例子了。

我提到视频游戏是 WebAssembly 的一个大目标，Unity 和 Unreal Engine 4 都已经有了工作演示。可以玩一局 [*坦克！*](https://webassembly.org/demo/) 在 Unity 引擎中运行 WebAssembly，Epic 已经[在线发布了一个简短的 WebAssembly 演示](https://mzl.la/webassemblydemo)。

[坦克！演示- WebAssembly](https://webassembly.org/demo/)

Figma 是一个运行在浏览器中的界面设计工具，允许设计师轻松地协作和共享他们的工作。它主要是用 C++编写的，有一个 2D WebGL 渲染引擎，可以处理非常大的文档。最初，他们使用 asm.js 为 web 编译 C++代码。通过切换到 WebAssembly，无论文档大小如何，他们的加载时间都缩短了 3 倍以上。

[WebAssembly 将 Figma 的加载时间缩短了 3 倍](https://blog.figma.com/webassembly-cut-figmas-load-time-by-3x-76f3f2395164)

[AutoCAD](http://blogs.autodesk.com/autocad/autocad-web-app-google-io-2018/) 是一种设计软件，主要用于各种工程领域绘制图纸，如平面图、电路、管道设计等。它是用 C++写的，已经有 35 年的历史了，比网络本身还长。由于 WebAssembly，它现在可以作为一个 web 应用程序使用，而不需要用另一种语言重写如此庞大的代码库。

你可以期待越来越多的应用程序使用 WebAssembly，网上也有一些有趣的演示，比如在浏览器中运行的[视频编辑器](https://d2jta7o2zej4pf.cloudfront.net/)、[光线跟踪器](https://mtharrison.github.io/wasm-raytracer/)和[面部识别算法](https://github.com/Web-Sight/WebSight)。

### 什么来了

浏览器已经在开发新功能。对**线程**和**垃圾收集**的支持即将到来，这将使 WebAssembly 成为更适合编译 Java、C#和 Go 等语言的目标。其中一个重要的目标是创建支持源代码映射的**调试工具**，这将允许开发人员轻松地将 WebAssembly 映射到他们的源代码。

JavaScript 仍将在 web 开发中占有一席之地。这是一种很棒的语言，足够灵活，几乎可以构建任何东西，那些它不能很好处理的漏洞现在可以用 WebAssembly 来填补。将 JavaScript 编译成 WebAssembly 是不可能的，而且也没有多大意义，因为浏览器已经被设计成直接使用 JS 并最大化其性能。

但是，即使您继续只使用 JavaScript，您仍然可以通过改进的库和框架从 WebAssembly 及其带来的速度提升中受益。很快你就可以像使用任何其他 ECMAScript 模块一样下载和[导入这些模块](https://www.youtube.com/watch?v=qR_b5gajwug&feature=youtu.be),并简单地从 JavaScript 调用它们的函数。至于框架，Ember 已经在调查其 [Glimmer VM](https://github.com/glimmerjs/glimmer-vm/pull/752) 的 WebAssembly 实现，并且也有可能在 WebAssembly 中实现一些 [React 特性。](https://www.youtube.com/watch?v=3GHJ4cbxsVQ)

未来就在眼前，看起来又快又光明🚀🌞

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/7521dd34c3bdf07adebf14236ec9e4ca.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *