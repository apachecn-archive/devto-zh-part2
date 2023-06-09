# 评估开发人员对编程语言的体验

> 原文：<https://dev.to/stereobooster/developer-experience-the-stereoboster-test-for-a-programming-language-4c44>

好吧，我需要解释一下。还有[“乔尔测验”](https://www.joelonsoftware.com/2000/08/09/the-joel-test-12-steps-to-better-code/)。这是一个评估软件团队的“3 分钟”测试。测试本身有点过时，比如“你用源码控制吗？”这就像问你刷牙了吗——每个人都应该毫无疑问地刷牙，但我仍然喜欢这个测试，认为它很有用。

所以我想我会想出同样的测试来评估编程语言的 DX 和围绕它的工具。

## 一种语言有包管理器吗？

有时软件是用汇编语言或穿孔卡片从头开始编写的。但是没有人再这样写软件了。

> 苏斯曼说，在 20 世纪 80 年代和 90 年代，工程师们通过组合简单且易于理解的部分来构建复杂的系统。SICP 的目标是为这类系统的推理提供抽象语言。
> 
> 如今，情况不再如此。苏斯曼指出，工程师现在例行公事地为复杂的硬件编写代码，他们并不完全理解(而且由于商业机密，往往无法理解。)在软件层面上也是如此，因为编程环境由具有巨大功能的巨大库组成。据苏斯曼说，他的学生花了大部分时间阅读这些图书馆的手册，以找出如何将它们缝合在一起以完成工作。
> - [戳编程:为什么麻省理工不再教 SICP](http://lambda-the-ultimate.org/node/5335)

今天的编程语言应该有一个包管理器，来安装额外的库和依赖项。

我想包管理器最好的例子之一是 [Bundler](https://bundler.io/) (Ruby)。很多其他的包管理器都受到了它的启发，比如 Cargo (Rust)、Yarn (Node)，我猜还有 Cocoapods (Objective-C)。

需要说的是，不是每个包经理都是一样的。好的软件包管理器有一些标准，例如，它应该能够缓存软件包(为了更快的安装)，它应该是确定性的(所以每次安装都是一样的)，它应该能够离线工作(一旦所有软件包安装完毕)。比如 npm v3 就是一个不太好的包管理器的例子，他们在最新版本中修复了很多问题。npm v3 是 yarn 存在的原因，它是如此难以使用，以至于脸书决定创建自己的包管理器。他们一直在改进它。

另一方面，如果该语言的作者不提供任何选项，社区会找到解决方法。我猜这就是 Cocoapods 是如何诞生的，这是一种解决方案(我不知道它现在有多流行)，但当社区分裂时，可能会有不太好的结果，例如，[Go 依赖管理的传奇](https://blog.gopheracademy.com/advent-2016/saga-go-dependency-management/)和 [Go 1.11 模块(vgo) vs dep](https://github.com/golang/dep/issues/1959) 。

**提醒**:我在这里的目的不是评判或指责，这只是一个关于事情如何出错的案例研究，并展示当没有关注 DX 时开发人员是如何挣扎的。

## 一种语言有代码格式化程序吗？

我猜语法是自行车脱落的首要原因。不是要带仇恨，只是说明能在这上面浪费多少精力:[bootstrap-drop down . js clear menus()需要；最后](https://github.com/twbs/bootstrap/issues/3057)。
如果没有官方风格指南社区会变得支离破碎，例如[“没有分号”是实用的反义词](https://github.com/standard/standard/issues/78)(又是 JS)。

有了好的代码格式化程序和 IDE 中的集成，你根本不需要担心格式化——你可以写任何东西，格式化程序会让它看起来更好。

格式化程序的示例:

*   来自戈朗的
*   JavaScript 的[更漂亮的](https://github.com/prettier/prettier)

值得一提的是，linter 不是 formatter 的替代品，例如 eslint。没有必要检查格式是错误的，并迫使开发人员修复它，当它是可能的简单地修复它。把所有无聊的工作留给电脑(是的，我知道 eslint 有`--fix`选项，但与`prettier`相比，它很慢而且不一致)。

有一篇关于这个主题的很好的科学论文——一台更漂亮的打印机，作者是菲利普·瓦德勒。那是他们在《漂亮的 T4》中使用的。

## 一种语言有版本切换器吗？

如果你在开发应用程序，你很可能不得不处理一个版本的语言。但是，如果您必须处理多个项目或开发库，您可能希望在同一台机器上安装多个版本的语言，这通常是一个问题，因为可执行文件的命名方式相同。这就是为什么你想使用某种“版本切换器”，应用程序或外壳功能，可以切换到不同版本的编程语言。

好的版本管理器可以:

*   根据目录中的首选项切换版本，这样每个项目都可以设置所需的版本。例如，`rbenv`将基于`.ruby-version`文件切换版本。
*   可以下载并安装所需的版本。例如，`rustup install stable-x86_64-pc-windows-msvc`

版本转换器的示例:

*   [rbenv](https://github.com/rbenv/rbenv)
*   [休息一下](https://github.com/rust-lang-nursery/rustup.rs)

值得一提的是，随着 Docker 的崛起，这个问题最近变得不那么重要了。

## 一种语言有静态分析器吗？

静态分析器——一个应用程序，在你将代码投入生产之前，它可以帮助你发现代码中的错误。它可以是防脚炮的棉绒或类型检查器。

有些语言是静态类型的，所以做类型检查很容易，但并不意味着不能对动态类型的语言做静态分析。渐进型系统有很多例子:

*   红宝石:[冰糕](https://sorbet.run/)、[雪糕](https://github.com/soutaro/steep)、[小菜蛾红宝石](https://github.com/stereobooster/diamondback-ruby)
*   JS:脸书流，TypeScript(可以和 JSDoc 一起使用)

另一方面，如果语言是静态类型的，这并不意味着类型检查器会发现所有错误，例如， [Infer](https://fbinfer.com/) 可以检测 Java 或 C/C++/Objective-C 中的潜在错误。

静态分析仪示例:

*   Bash: [shellcheck](https://www.shellcheck.net/)
*   JS:ESlint with[ESlint-config-react-app](https://www.npmjs.com/package/eslint-config-react-app)
*   nginx 配置: [gixy](https://github.com/yandex/gixy)

## 一种语言是否提供了清晰的错误信息？

[我之前写过这个](https://dev.to/stereobooster/developers-experience-error-messages-5cbn)。我不会重复我自己，除了我认为[榆树](http://elm-lang.org/blog/compiler-errors-for-humans)是这一类中最好的例子。

## 一种语言有调试器吗？

### 命令式语言

如果语言是必要的，你可以[将打印输出到标准输出](https://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html)或`console.log`。这并不是一个理想的解决方案——我记得用`alert`在 Internet Explorer 中调试 JS 的恐怖故事，后来 Firebug 出现，是对 DX 的大改进，然后是 Chrome DevTools 协议。

有时可以使用“通用的”(非特定语言的)工具，如`GDB`或`strace`，但这可能很难，最好使用特定语言的工具。

调试工具的示例:

*   调试 Linux 性能问题: [linuxperf](http://www.brendangregg.com/linuxperf.html)
*   调试 HTML/CSS/JS 性能问题: [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/)
*   调试微服务: [Jaeger](https://www.jaegertracing.io/docs/1.7/#trace-detail-view)
    *   调试 goroutines [gotrace](https://github.com/divan/gotrace)
    *   调试垃圾收集器:

### 陈述性语言

但是如果语言是声明性的，你肯定需要一些工具，否则，你就完了。SQL 是声明性语言的一个很好的例子，`explain`是调试工具，它会帮助你找出查询慢的原因。 [PostgreSQL 工具特别好](http://tatiyants.com/postgres-query-plan-visualization/)。

另一方面，CSS(也是声明性语言)没有好的调试器，因此，开发人员很难对付它。

*   [https://twitter.com/thomasfuchs/status/493790680397803521](https://twitter.com/thomasfuchs/status/493790680397803521)
*   [https://twitter.com/chriscoyier/status/919798232179511296](https://twitter.com/chriscoyier/status/919798232179511296)

这方面有一些著作:

*   [农药](http://pesticide.io/)
*   [CSS 触发器](https://csstriggers.com/)

## 语言有标准库吗？

这是一个有点含糊的问题。所以我不会给出定义，而是给出一些例子。

不要:

*   OCaml 标准库很难工作，所以大量使用 OCaml 的公司编写了他们自己的库。
*   [在 Go 中有些东西可以标准化，而不是一遍又一遍的写](https://twitter.com/garybernhardt/status/905945308202287104)
*   JS 缺乏处理日期的标准库，所以[人们创建了一个大型库来处理它](https://momentjs.com/)。你猜怎么着？接下来，有人抱怨它太大了，影响了加载时间。

做:

*   我猜是铁锈
*   也许某种程度上是鲁比

## 一种语言有学习资源吗？

在某种程度上，你需要让初级开发人员加入你的项目，你需要教导和帮助他们适应。这些学习资源首先将为高级开发人员节省时间，他们可以发送一些课程的链接并回答一些问题，而不是阅读本课程。

再次，有点模糊的定义，所以我会提供例子:

不要:

*   [流量类型](https://flow.org/)。除了官方文件，我找不到任何有用的东西

做:

*   打字稿。我在 egghead.io 和其他网站上找到了很多资源和介绍材料

## 一种语言有最佳实践列表吗？

一个新手的典型问题:X 用什么，其中 X 可以是 HTTP 请求，HTTP 服务器，流实现，外来数据结构，ORM 或者其他什么。当前的最佳实践是什么？

不要:

*   众所周知，JS 的选择太多了。甚至还有一个术语来形容它 [JavaScript 疲劳](https://www.quora.com/What-is-JavaScript-fatigue)。随着 create-react-app 对配置趋势的一些约定，情况最近有所好转，出现了 [bestofjs](https://bestofjs.org/) ，但仍然很难

做:

*   一个很好的例子来自 Ruby 世界。还有 [ruby-toolbox](https://www.ruby-toolbox.com/) 。(事件发生后)现在不太光彩，但相信我，[那是](https://web.archive.org/web/20170430093635/https://www.ruby-toolbox.com/)。

## 语言环境容易设置吗？

这并不总是这种语言的特点，有时更像是这种语言的项目的特点，但这绝对是一种趋势。公平地说，随着码头工人的增加，这不是一个问题，但仍然。

一些 C 和 C++项目有一些荒谬的需求，比如你需要`make`、`cmake`、`GYP`、`python`、`node`一些动态库，所有这些都应该有确切的版本，否则就是不兼容的。我知道有一个原因，有确切版本的动态库(或操作系统特定的库)，但除此之外，它只是坏的 DX。

## PS

这是第一稿(准确地说是第二稿，前一稿在我以前的博客上)。我想在得到反馈后，我需要更新它。我错过了什么吗？

我希望我说清楚了——我不想羞辱或指责那些语言或工具中的任何一个，我只是想展示什么是不好的 DX，什么是好的 DX，这样人们就可以学习和提高。如果你觉得我越界了，请告诉我。

*Unsplash 上 rawpixel 拍摄的照片*