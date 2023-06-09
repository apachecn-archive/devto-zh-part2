# 最后是一个二进制网络

> 原文：<https://dev.to/elyalvarado/finally-a-binary-web-26fk>

## WebAssembly 如何产生的简短而不完整的历史

*本文是关于 WebAssembly 系列文章的第一篇，基于我在[麦德林](https://medellinjs.org):*[web assembly 101](https://wasm101.surge.sh)*上的演讲。*

> 那些不记得过去的人注定要重蹈覆辙。

乔治·桑塔亚纳的这句名言在计算机科学中有着特殊的意义。我在这个领域已经呆了足够长的时间，看到模式和技术成为一种时尚，然后被新的更闪亮的技术取代，仅仅几年后又作为下一个大事件回来。函数式编程范例正在发生这种情况，它在蛰伏了 40 年之后正在经历复兴。不要误解我，我并不反对这个循环，我认为它需要不断进化。我不喜欢因为每个人都在谈论最新的东西，就抛弃所有的东西，转向最新的东西。反正这不是本文的重点。

我喜欢看事物的历史，它们是如何形成的。这就是为什么当我第一次对 WebAssembly 感兴趣时，我想知道它是如何起源的，这个探索带我回到了万维网的起源。让我带你看一下。

### 1995 年

那是 1995 年。微软为其革命性的新操作系统 Windows 95(T1)发起了一场大规模的营销活动，T2 的美国在线(T3)和 T4 的神童(T5)开始首次提供访问万维网的服务。

[![Netscape Navigator 2.0](img/af84ee0f5c8a488efff6708a09123391.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9VYC3G8---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8prb4qloclqcuj0c7737.png)

今年年底，网景公司推出了他们浏览器的 2.0 版本:网景导航器。这一版本包括了多项改进和新特性，其中有两项技术将彻底改变网络，我们现在仍在使用:Javascript，当然还有动画 GIF。

Javascript 是由 Brendan Eich 在 1995 年 9 月[只用了 10 天](https://www.computer.org/csdl/mags/co/2012/02/mco2012020007.pdf)就创造出来的。虽然它最初被称为 Mocha，后来被称为 LiveScript，但它被重命名为 Javascript。这是一种营销策略，旨在利用围绕 Sun 公司的微系统 Java 编程语言而产生的日益增长的人气和期望。不幸的是，这个决定引发了历史上最大的编程语言命名混乱，其影响至今仍被世界各地的人力资源部门所感知。

最初，Javascript 旨在成为一种“粘合”语言，供非正式开发人员和设计人员用来连接 Java applet 组件和自动化交互。当时的普遍看法是，任何高级开发都应该作为 Java 中的小程序来完成。

### 1996 年

随着万维网的普及，在 1995 年和 1996 年之间增加了一倍多，开发人员转向不同的解决方案来利用该平台。

[![Java](img/23c26125a2484107974a676b30a32479.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j9BaB1gy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w2x5dpievyg2i1grm1sv.png)

同年公开发布的 Java 承诺完全的可移植性，并以它们的小应用程序瞄准了网络。现在很容易讨厌 Java 小程序这种技术，但是很长一段时间以来，它都是以跨平台的方式将应用程序编程引入 web 的首选方式。

[![ActiveX](img/862a928409c7490b180d6e587b3b337a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--de5hoq-P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7d6do6f451cjmc30307q.png)

当然，微软也提供了 Java 小程序的替代品。通过在他们的 Internet Explorer 3.0 (1996)浏览器中引入对 ActiveX 控件的支持，他们打算利用 Windows 开发人员将他们的代码移植到 Web 上的渴望。ActiveX 很难保护，也不像 Java 小程序那样可移植，但它在企业内部网应用中非常成功，我敢打赌，在大企业或政府公司中仍然有一些应用程序使用它，并且只在 Internet Explorer 上运行。

[![Flash](img/44772a7faa365db000ca525c991eab5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sv9l-9am--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ms3eghocj5hunih90ua.png)

同年，一家名为 Macromedia(后来被 Adobe 收购)的小公司发布了他们的 Flash web 插件，该插件拥有用于创建多媒体内容的优秀创作工具。Flash 风靡网络。到 2005 年，几乎 98%的电脑都安装了 flash 插件，如果不是史蒂夫·乔布斯顽固地不支持其 iOS 平台的 flash 插件，它可能仍在使用，而不是 HTML5/Javascript。

### 2004 年

即使从最坏的想法中，有价值的东西也能被拯救出来。1999 年，在开发 Microsoft Exchange 2000 邮件服务器时，Outlook Web App 团队开发了 ActiveX XML-HTTP 组件。这个想法很棒，首次开启了无需重新加载网页就能与服务器通信的可能性。这是一个如此强大的想法，以至于不久它就被所有的浏览器厂商所采用:到 2002 年，Mozilla 已经在其 Javascript API 中实现了它的一个版本，称之为 XMLHttpRequest。两年后 Safari 也实现了它。

[![Ajax](img/04fa17ceb03f867c1a650eebfd9cb516.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rMB4DkFj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ibei8xpgynewme2gq2bs.png)

javascript 对 XMLHttpRequest 的支持在后来被称为 Ajax 的一系列技术中发挥了重要作用，它彻底改变了可以用 Javascript 开发的应用程序类型。

然而，在那个时候，很少有开发人员理解 Javascript 创建复杂应用程序的潜力，直到同年谷歌推出 Gmail，Javascript/AJAX 应用程序的真正潜力才开始显现。

### 2013 年

随着复杂 javascript 应用程序和更大代码库的出现，Javascript 的执行速度开始变得重要起来。

在 2008 年底和 2009 年初，推出了新的浏览器引擎:V8、TraceMonkey 和 Nitro。可以说，浏览器制造商之间的性能战争开始了，因此，JIT 编译器和动态代码优化等新技术被引入，并使 Javascript 代码执行更快，在某些情况下接近原生执行速度，这在以前是不可想象的。

到 2013 年，浏览器已经非常快了。然而，开发人员希望从该平台获得更多。此外，存在大量已经编写的 C/C++代码，很难将其移植到 JavaScript。

为了将 C/C++开发引入浏览器，谷歌在 2014 年推出了他们的 Native Client (NaCl) SDK。NaCl 允许编译 C/C++代码，以便在浏览器上使用。然而，他们的安全模型和通用架构使得它很难移植到 Chrome 之外的其他浏览器引擎上，并且被其他供应商拒绝。

与此同时，Mozilla 的一组开发人员正在开发 ASM.js:作为编译目标的 Javascript 子集。它包括类型提示，旨在使浏览器上的类型推断和代码优化更快。ASM.js 允许将 C/C++代码编译成可在所有浏览器中部署的 Javascript。这种方法虽然比 NaCl 更具可移植性，但无法保证跨浏览器的一致性能，并且不同浏览器引擎之间的执行速度差异巨大是不变的。

### 2015 年

Google 和 Mozilla 几乎同时致力于为 web 编译 C/C++，这表明需要一个标准的解决方案来解决这个问题。

[![WebAssembly](img/f6301898efafd18c97ba91f359c03f6c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3M2t5C85--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2v87r20os4o070bszljb.png)

2015 年 6 月 17 日，Javascript 创始人 Brandon Eich 和 Mozilla 的 Luke Wagner(ASM . js 的创始人之一)宣布开始开发 WebAssembly。

这是前所未有的，意想不到的。4 个主要浏览器引擎的团队在 CSS 属性的语义上有一段艰难的时间达成一致，设法就 Web 的标准二进制格式达成一致:WebAssembly。

更令人难以置信的是发展速度。不到一年后，2016 年 3 月 15 日，WebAssembly 在 Firefox、Google Chrome 和 Microsoft Edge 中演示运行 [Unity 的愤怒机器人](https://beta.unity3d.com/jonas/AngryBots/)。

### 2017

2017 年 3 月，MVP 的初始阶段宣告完成。并且到 2017 年 9 月底，Safari 11 包含了对 Wasm 的支持，最终完成了对主要 4 个浏览器引擎的支持，使 WebAssembly 成为第一个在 Web 平台上被普遍支持的二进制格式。

### 2018 年与未来

我们慢慢看到了 WebAssembly 对网络的巨大影响。Autodesk 刚刚推出了一个利用 WebAssembly 的 Autocad web 版本，两个主要的游戏引擎都已经支持它作为 WebGL 的默认编译目标。

然而，我们才刚刚开始，这只是 MVP 版本。你能想象 WebAsembly 的下一个大用途是什么吗？它会引发网络上的第二次性能革命吗？

请继续关注下一篇文章，在那里我将讨论 WebAssembly 的未来。