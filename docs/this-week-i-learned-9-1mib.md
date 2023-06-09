# 本周我学习了第九条

> 原文：<https://dev.to/johnpaulada/this-week-i-learned-9-1mib>

[![This Week I Learned](img/3fd5fb67b3d8f747c685019fb3f2a270.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7sHzyEtU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ALajC4RIKn5A-4zmlSCXb9Q.jpeg)

> 关于测试、Python、ReasonML 等有趣的东西和公告。在本周的这一集里，我学到了！

[![Cypress](img/13e8c3ac44750d9e03ded7240622e48a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---DK7deu6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/PqwhJgd.png)

## [柏树](https://www.cypress.io/)

> 快速，简单和可靠的测试任何在浏览器中运行的东西。

Cypress 是一种新的 web 应用方式。就我个人而言，我认为它取代了我的 Selenium 用例，而且更快、更漂亮、更容易使用。以下是我最喜欢的柏树的特征:

*   不使用 web 驱动程序。
*   它有一个漂亮的图形用户界面。
*   您使用 JavaScript 编写 Cypress 测试。
*   断言语法类似于其他 JavaScript 测试库，如 Chai 和 Jasmine。
*   你不需要明确地告诉它等待。它会自动这样做。
*   当您更改测试代码时，测试会重新加载。
*   您可以在测试发生时看到它们，并且可以在测试过程中后退一步。
*   如果您不使用 GUI 运行它，它会创建测试过程的视频。

[![face_recognition](img/e2c709d0340a1db9e68348127f5674b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n2vW2mGO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/TRQb4pk.png)

## [人脸识别](https://github.com/ageitgey/face_recognition)

> 世界上最简单的用于 Python 和命令行的面部识别 api

如果你想做面部识别并且你知道 Python，不用再找了。Adam Geitgey 的人脸识别库非常容易使用，而且非常准确。只要查一下文件，你马上就会知道你需要做什么。

[![Sketch.sh](img/98d7ea4b97c0cd0c82eacf485e0bfd1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vayikmKm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cZBPkgz.png)

## [T1](#sketchsh)[sketch . sh](https://sketch.sh/)

如果你以前使用过用于 Python 或 R 的 Jupyter 笔记本，或者 RunKit、ObservableHQ Scratchpad 和用于 JavaScript 的 Kajero，你会同意它们是用于实验和分享你的工作的非常好的平台。

如果你对 Reason 和这类笔记本感兴趣，你会对 Sketch.sh 非常满意。Sketch 允许你创建 ReasonML 和 OCaml 交互式笔记本。如果你想交互式地教和学 ReasonML，这将是很棒的，特别是因为它是相当新的。

## Doc 测试中的药剂

您是否曾经编写过文档，并发现文档中的代码示例因为代码更新而不再有效？

Elixir 通过我们所谓的 doctests 解决了这个问题。基本上，当您在文档中添加代码片段并将其配置为与 doctests 一起工作时，Elixir(特别是 ExUnit)将确保代码片段正常工作。如果值不是预期的，测试将会失败。这确保了您拥有的文档实际上与您编写的软件相匹配。

相当酷！又一个改用仙丹的理由！😄

[![mdx-deck](img/ebe07514939e03967212cd38e8d01f2e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bMzSvlHg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vwgs3t8dbu5dz2ijwume.png)

## [mdx-甲板](https://github.com/jxnblk/mdx-deck)

如果你像我一样是个极客，你喜欢用代码创建幻灯片，因为这很酷！但是用 HTML 之类的东西来构建它有点耗时而且复杂，所以我们有时只是求助于使用 Markdown 来使事情变得简单。但是降价并不是万能的。如果你想增加互动性，游戏结束了。

幸运的是有 [mdx](https://mdxjs.com/) ，它允许我们用 JSX 创建降价文档！现在，我们可以在文档中添加交互性和“web 组件”。现在，mdx 允许我们创建交互式文档，但幻灯片呢？mdx-deck 为我们解决了这个问题，它允许我们使用 mdx 语法创建幻灯片。因此，我们可以创建一些 React 组件，并在幻灯片中展示它们的运行情况。您甚至可以使用包含的主题来更改主题，或者创建自己的主题。用 mdx-deck 制作您的幻灯片，祝您愉快！

[![reason-maybe](img/7774ad111ae16d36893e858ef4a02688.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cj-797MV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0l5b9rjzif5gnxw3z1p9.png)

## [原因——也许是](https://redex.github.io/package/reason-maybe/)

我已经使用 ReasonML 有一段时间了，所以我尝试在 Reason 中构建一个库并不奇怪。所以，我构建了 reason-maybe，这是 reason 中的一个 maybe 单子实现。大多数情况下，你可以在 Reason 中只输入可选类型，但是如果你想要链/平面图等。，这是一个很好的尝试。

[![Stencila](img/c61a440d365c8ed16b07136f56ed7aed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yVJ2RHTB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cdo7aj1wcbksmkzua2rt.png)

## [模版](https://stenci.la/)

Stencila 是另一个用于建立可重复研究的交互式笔记本软件。这结合了 MS Excel 和 Word 之类的东西，加上用 Python 和 Node 编码的能力。看起来也干净一点。这绝对是一个非常有趣的尝试，尽管我觉得它还没有完全准备好。但是一定要试一试。

[![DryStack](img/f86e6dc35986872e87dae85f5e6e4dd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--klFjxv09--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bofkevjcvie5wlgufwfb.png)

## [干栈](https://drystack.io/)

如果你很难为你的项目选择堆栈，或者你不知道你的选择是什么，你可以试试 DryStack。您可以查看一些针对客户端、服务器、工具等的技术堆栈选项。如果你不知道从哪里开始，这是一个很好的起点。

[![FlacheQL](img/0825300becd85eb9f59be6b6e1a349dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zQlyf5TI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pd4wq66znw1ezpyo9xql.png)

## [FlacheQL](https://github.com/FlacheQL/FlacheQL)

GraphQL 是一项年轻的技术，仍然有许多问题没有行业标准的解决方法。缓存中的一个问题。虽然脸书已经为此创建了一个解决方案，即 dataloader，但它只缓存确切的请求。一个新的竞争者 FlacheQL 允许部分检索缓存的结果。这确实很好，因为如果查询与缓存的查询不完全匹配，您不必获取所有内容。这将使 GraphQL 的缓存更加高效。

[![Kore](img/780bc62fbbc29f8909cab75d42d0ad31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6y704Brv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t4insqdjey2ccmhgwkyh.png)

## [科勒](https://kore.io/)

如果你厌倦了通常的 PHP/Java/C#/Node 方法来开发 web APIs，想要一些更核心的东西，试试 Kore 吧！它允许你用 c 语言构建 web APIs。

英寸 C.

如果这还不够硬核，我不知道你对硬核的定义是什么。

但这仍然是一个有趣的概念，我们都知道 C 有多快。所以，如果你敢的话，一定要试试看。