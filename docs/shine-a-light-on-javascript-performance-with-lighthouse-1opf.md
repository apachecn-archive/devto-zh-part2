# 用 Lighthouse 照亮 JavaScript 的性能

> 原文：<https://dev.to/addyosmani/shine-a-light-on-javascript-performance-with-lighthouse-1opf>

不确定 JavaScript 的[成本对你的用户体验来说是否太高？🙃](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4) [Lighthouse](https://developers.google.com/web/tools/lighthouse/) 有一个 [JavaScript 执行时间审计](https://developers.google.com/web/tools/lighthouse/audits/bootup)，它测量 JavaScript 对页面负载性能的总体影响:

[![](../Images/2a7e2e61da11933451458e3325e2e18f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sglLOF1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ggbnzgr8b1k8suklbjsr.png)

试试看。今天它出现在 Chrome DevTools [审计](https://developers.google.com/web/updates/2017/05/devtools-release-notes#lighthouse)面板中。它也可以通过[网页测试](https://webpagetest.org/easy)获得。

对于上面的内容站点，浏览器在移动设备上处理这个站点的主包需要 51s (oi vey)。包括网络传输时间在内，用户可能要等待一分钟才能与该页面进行交互⏳😪

[![](../Images/cad1d76c2c1f1ab16f5c32cfbfd3cc5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vm1znBte--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oqmf5qnqt89lt5f81pub.png)

这是在中等移动设备配置上解析、编译和执行脚本所花费的时间。 [dev.to](https://dev.to) (提供类似的内容体验)能够加载他们的主包，对脚本执行❤️的依赖性最小

[![](../Images/146657794f302bd39c47ebabd3b6c787.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZzfpjOGJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/detuj3z8swkr729nb6zs.png)

如何才能提高原站点 JS 的成本？

只发布用户真正需要的 JavaScript。我们可以根据需要使用像[代码分割](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/code-splitting/)这样的技术来延迟加载其余的。我使用 DevTools [代码覆盖](https://developers.google.com/web/updates/2017/04/devtools-release-notes#coverage)特性来帮助这里。

[![](../Images/d6d83e1f4f791a245a5077a13b4f87c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gW_GJhzR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ksqhn42t3sswm0oah9zd.png)

如果我点击 record 并加载上述体验，然后交互一会儿，我们可以看到大约 57%的预先加载的代码可能并不需要。对于可以按需加载的东西来说，这是一个很好的选择。

如果你以前没有看过 Lighthouse，它充满了有用的信息，比如检查你是否正确地缩小或压缩了你的脚本:

[![](../Images/5121de4b636eb81a5fccf5fe9d231b8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vN61H7CR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uwsg5tivewa8plw9o8jt.png)

如果你对使用无头 Chrome 的自动化感兴趣，还有一个有用的[代码覆盖示例](https://github.com/GoogleChromeLabs/puppeteer-examples#code_coveragejs)给[木偶师](https://developers.google.com/web/tools/puppeteer/)，它可以可视化 JS 代码覆盖在页面加载中的使用。

包扎..🎁

JavaScript 对你的用户体验有很大的影响；Lighthouse 可以在这里突出改进的机会。要降低 JavaScript 的传输和处理时间:

*   只发送你的用户需要的代码。
*   缩小并压缩你的脚本。
*   移除未使用的代码和依赖项。