# 用桥支撑撞墙，用 Upwork 求助

> 原文：<https://dev.to/progrium/hitting-a-wall-with-bridgesupport-using-upwork-for-help-3nad>

星期二，我致力于为函数、接口和类生成常量、枚举和存根。因此，它正在生成整个 Foundation 和 AppKit 框架的框架。不过，在深入讨论之前，我发现 bridgesupport XML 文件没有属性，也没有它们的 getters 和 setters。给定一个像“sharedApplication”这样的已知属性，我甚至无法在 XML 中找到它的引用。因此，桥支持文件不会有所帮助。

这使我回到了利用文档的默认策略。理论上，我不需要解析它。我可以让文档生成器生成结构化数据。Doxygen 可以解析 Objective-C 头文件，所以我正在考虑这个问题。或者，如果它不起作用，我们可以直接解析源代码，但我喜欢这个想法，即*而不是*必须为 Objective-C 编写一个语言词法分析器/解析器，天啊。

问题是，即使这样感觉也很无趣，而且我已经在这方面达到了极限。但这是一个定义明确的问题，所以我把它发布到 Upwork 上。实际上我找到了一个可能完美的人。他做了很多类似的事情，并且在一个叫做 Babelfish 的项目上工作，这个项目可能对将来其他语言中的类似内容非常有用。他还为 Go 编写了一个 DOM 绑定，我正在用 WASM 原型查看。疯狂的巧合！

所以，希望我现在能得到一些帮助，因为从长远来看还有很多工作要做，我已经有足够的工作了。
[https://www.youtube.com/embed/1y9-WPUw1uQ](https://www.youtube.com/embed/1y9-WPUw1uQ)T2】