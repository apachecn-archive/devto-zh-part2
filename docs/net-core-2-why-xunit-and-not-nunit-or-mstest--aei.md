# NET Core 2:为什么是 xUnit 而不是 NUnit 或 MSTest

> 原文：<https://dev.to/hatsrumandcode/net-core-2-why-xunit-and-not-nunit-or-mstest--aei>

随着最近一个使用 NET Core 2 的新项目，我和我的团队研究了我们是否应该转移到 MS Test(当时没有考虑 MS Test 2)，坚持使用 NUnit 或尝试 xUnit。我们遇到了一个高峰，我研究了如果我们不能使用 xUnit，我们是否还可以使用 NUnit，因为我们并不热衷于将 MSTest 作为替代框架。最后，我们决定尝试一下 xUnit！

与 NUnit 和 MsTest 相比，xUnit 相当精简，而且是最近才编写的。的。自从 NUnit 第一次被创建以来，NET 框架一直在发展。XUnit 利用一些新特性来帮助开发人员编写更干净的测试，因为测试应该保持干净，并被视为一等公民。作者想编纂一些规则，而不是重复关于“做 X”或“不做 Y”的指导。你可以在这里了解更多关于为什么创建 xUnit:[https://xUnit . github . io/docs/why-did-we-build-xUnit-1.0 . html](https://xunit.github.io/docs/why-did-we-build-xunit-1.0.html)。

**我们选择 xUnit 的一些原因:**

*   NUnit 与不完全兼容。当时的网络核心 2
*   我们想摆脱 MS Test，因为团队更喜欢 xUnit 和 NUnit 编写测试的方式
*   xUnit 旨在改善测试隔离，并试图编纂一套规则来建立测试标准。
*   xUnit [Fact]和[Theory]属性是可扩展的，因此您可以实现自己的测试功能。vsmdi 文件来跟踪您的测试。
*   微软正在内部使用 xUnit，它的创建者之一来自微软。xUnit 也是 NUnit 的原作者之一创作的。
*   没有[Setup]和[Teardown]属性，这是使用 test 类的构造函数和 IDisposable 完成的。这鼓励开发人员编写更干净的测试。
*   xUnit 允许我们写更少的代码，因为它的灵活性允许像 subspec 这样的东西，允许你只写你需要做的事情。使用的工具有 xBehave:[http://xbehave.github.io/](%5Bhttp://xbehave.github.io/%5D(https://reed.jiveon.com/external-link.jspa?url=http%3A%2F%2Fxbehave.github.io%2F).)和 [Xunit 等。小黄瓜快](https://github.com/ttutisani/Xunit.Gherkin.Quick)。
*   xUnit 更容易阅读，使用直观的术语。

鲍勃大叔最近一篇关于(单元)测试的有趣文章:
[http://blog . clean coder . com/Uncle-Bob/2017/05/05/test definitions . html](http://blog.cleancoder.com/uncle-bob/2017/05/05/TestDefinitions.html)

在选择了我们的测试框架几个月后，发布了一个性能(Visual Studio 2017 的新更新)对比，比较了 NUnit、xUnit 和 MS Test 2。你可以在这里找到微软的博客文章。

**参考文献:**

[http://georgemauer.net/2015/05/01/why-not-mstest](http://georgemauer.net/2015/05/01/why-not-mstest)
[https://seankilleen.com/2015/06/xUnit-vs-MSTest/](https://seankilleen.com/2015/06/xUnit-vs-MSTest/)
[https://stackify.com/unit-test-frameworks-csharp/](https://stackify.com/unit-test-frameworks-csharp/)
[https://xunit . github . io/docs/why-did-we-build-xunit-1.0 . html](https://xunit.github.io/docs/why-did-we-build-xunit-1.0.html)[http://blog . clean coder . com/大叔-bob/2017/05/05/test definitions . html](http://blog.cleancoder.com/uncle-bob/2017/05/05/TestDefinitions.html)