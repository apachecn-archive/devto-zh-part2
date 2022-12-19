# 设计系统:使用模式库维护设计

> 原文：<https://dev.to/nothing/design-systems-maintaining-design-with-pattern-libraries-1e70>

[![](../Images/f3792d518c372355e45bb5a6d77c62b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1aF6Yusq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k0t581zg59095foymshy.png)

网络是构建软件的一个不可思议的平台:你可以从几乎任何地方获得大量信息，这给了开发者很大的范围来构建几乎所有东西——从简单的静态网站到 3D 游戏。你可能会感到惊讶，但是维护软件最具挑战性的部分之一是保持设计和代码彼此同步。在这篇文章中，我想强调在开发大中型 web 应用程序时，我们如何使用模式库来维护设计和代码之间的流畅工作流。

术语“设计系统”比以往任何时候都更流行。自从谷歌的[材料设计](https://material.io/)和其他几个[大公司向公众分享他们的设计系统](https://designsystemsrepo.com/design-systems/)以来，围绕设计如何维护的争论在互联网上展开。如果你从未听说过这个术语，[这里有一篇很好的文章解释了什么是设计系统](https://medium.muz.li/what-is-a-design-system-1e43d19e7696)。简而言之:

> Design systems 是一个有益于软件、通信、人员和业务的设计指南。

设计系统的一个重要部分是所谓的模式库。虽然设计系统根据动画和动作设计、内容原则、可访问性和信息架构(包括模式库)来定义整体设计，但模式库单独定义了设计系统的组成部分及其连接方式。

这样，模式库有助于隔离设计的单个元素，同时提供一个平台来保持所有部分的概览。**这对于保持设计的长期一致性至关重要**。例如:在 web 应用程序中，模式库是一组具有相应样式和功能的 HTML 元素——其中之一可能是在按下“继续”按钮时关闭的确认对话框。

[![Screenshot of a material design dialog](../Images/871df210da1ad86f116801378f2f5ddf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KKmqA3us--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALfuaz7wObeRrxLi7WOI2NA.png) 

<figcaption>对话框组件中的[材质样式向导](https://material.io/develop/web/components/dialogs/)。</figcaption>

## **使用模式库的四个理由**

让我们来看看为什么模式库是设计系统的一个重要部分，并且会帮助你的项目的四个原因。

**1。一致性**

对于小型的个人网站来说，模式库可能不值得投资。但是对于那些会随着时间的推移而增长并且需要维护几年的 web 应用程序来说，模式库有助于保持设计的一致性。此外，它**提供了所需的概述，说明设计的哪些部分在代码形式**中是可用的，并防止开发人员重复自己并失去时间和注意力。

**2。处理依赖关系**

模式库有助于开发人员考虑网站各个部分的隔离。通过这种方式，可以防止**不必要的依赖性**。设计 web 应用程序最常见的错误之一是新功能开始借用其他功能的代码。在扩展或更改 web 应用程序的后期阶段，这通常会由于无意中更改或破坏应用程序的某些部分而导致问题。

**3。一次构建，随处应用**

模式库的一个通用规则是[保持技术不可知](http://bradfrost.com/blog/post/managing-technology-agnostic-design-systems/)。这意味着无论您使用什么框架或工具来构建 web 应用程序，**模式库都尽可能保持原始格式**——例如 HTML、CSS 和 JavaScript。

原始模式库可以很容易地应用于任何用于构建最终 web 应用程序的技术栈。例如，如果您决定在稍后阶段切换技术堆栈以提高性能或可维护性，那么您将需要更少的时间来将设计应用到新堆栈，因为您不必为新堆栈重写所有部分。此外，使模式库技术不可知有助于不直接在前端工作的开发人员。他们可以轻松地将该设计应用到其他系统，而无需深入研究前端代码的所有部分。

**4。为设计师提供一个测试**的平台

模式库为设计者提供了一种简单的方法来测试编码的设计是否符合他们的概念。原因是它为**提供了一个简单的平台，可以隔离和显示完成的组件**——或者单独显示，或者与其他元素和组件结合显示。最后但并非最不重要的一点是，它还可以用作最终 web 应用程序之外的单个组件的自动测试解决方案。

> 设计系统和模式库中最大的错误可能是它们没有得到维护。

## **看一个具体的例子**

在过去的几年里，我们已经建立了几个模式库。迄今为止最大的一个用于我们为[**Klett und Balmer Verlag**](https://www.klett.ch)设计和开发的用户界面。Klett und Balmer Verlag 是瑞士最大的教育出版公司之一。他们为学校和个人提供书籍。与负责后端的 [H2G Internetagentur](https://www.h2g.ch) 一起，我们重新设计了现有的体验，目前正在实施新的设计。让我们看看我们是如何使用模式库的。

首先，他们的网络服务被分成三个独立的平台。它们共同组成了 klett.ch:

1.  **网店**:提供所有可购买的产品。
2.  **用户部分**:用户设置和选项。
3.  **网站**:信息和文章。

所有这些部分可能在后台使用不同的技术。此外，它们连接到不同的数据源，由不同的人管理。但是它们有一个共同点:它们都属于 Klett und Balmer 出版社，因此在设计的许多方面都有相同之处。在不实际提供任何后端逻辑的情况下设计和开发新的用户界面产生了几个隐含的要求:

1.  以后端开发人员可以轻松应用到他们的堆栈中的形式提供前端。
2.  维护用户界面的单一代码源。
3.  当用户界面发生变化时，允许轻松集成到后端系统中。

当您想要使用技术不可知的方法时，最后一个需求是一个特别棘手的需求。让我们看看我们是如何做到的。

## **工具:Zeplin +模式实验室**

第一步，设计师们准备通过 [Zeplin](https://zeplin.io) 提供他们的设计，这是一种在线服务，允许**将设计源文件**与安装在电脑上的一个小助手工具同步。设计者创建的屏幕和组件(例如在 Sketch 中)然后在线可见，并允许开发者与它们交互；例如，通过复制 CSS 样式或查看特定元素的度量。

然后由开发人员完成代码转换。他们通过使用模式库工具来构建 HTML、CSS 和 JavaScript 中的组件和特定模式。创建模式库有很多选择。然而，他们中的许多人将开发者限制在特定的规则上:例如给定的文件夹结构或者必须使用的模板引擎。

几年前，我们决定使用[模式实验室](https://patternlab.io)，这是一个帮助创建网络模式库的工具。模式实验室由[布莱恩·穆恩森梅尔](https://www.brianmuenzenmeyer.com/)、[埃文·可爱](http://www.evanlovely.com)、[布拉德·弗罗斯特](http://bradfrost.com/)以及来自世界各地的优秀开发人员组成的团队带来了生机。这是现存时间最长的解决方案之一。因此**你可以相信它已经过很好的测试**用于生产用途。

Pattern Lab 的另一个好处是:**它给了我们很多自由**去构建我们的模式库。提供了不同的模板引擎，如[小胡子](http://mustache.github.io/)和[小枝](https://twig.symfony.com/)，它将在未来允许[手柄](http://handlebarsjs.com/)。

同时，H2G Internetagentur 的后端团队决定与 [Symfony](https://symfony.com) 合作。幸运的是，一些依赖项，最重要的是网站的 CMS，与 Twig 配合得很好。这使得使用 Pattern Lab 的 Twig 版本并尝试构建可以在 Symfony 环境中直接使用的组件变得容易。

[![Visualization of the tools and connections](../Images/7c199130dbaf1c560384ba733ec663b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gt5hl7DE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/926/1%2ADRzfWdzXI43PrTetLS1Dmw.png) 

<figcaption>从静态设计到动态代码:我们用草图来创造设计，Zeplin 作为设计师和开发者之间的桥梁。</figcaption>

的确，这种组合并没有完全实现模式库的技术无关构建的理想。但是它极大地提高了 Klett und Balmer Verlag 的特定项目的第三个要求。最重要的是，它解决了更新过程中出现的问题，并要求后端开发人员对标记进行更改。这再次允许我们直接修复问题，并将代码应用到测试环境中，而无需后端开发人员的进一步代码交互。

## **建立图案库:从最小块到最大块**

虽然 Pattern Lab 的创建者经常提到[原子设计](http://bradfrost.com/blog/post/atomic-web-design/)，但我们决定使用一种类似的、但略有不同的方法。我们将代码分为以下几个部分:

1.  **组件**:单个组件，如按钮或表单域。它也可以和其他组件结合，比如包含按钮的对话框。
2.  **模式**:这些是特定的用例，其中组合了多个组件，例如登录表单、页眉导航或页脚。
3.  模板/布局:一旦我们组合了不同的模式，比如导航、网站页眉和网站页脚，我们就得到了一个模板。在我们的例子中，我们为商店和网站使用了不同的模板。
4.  **主题**:应用品牌特定的定义，如颜色和字体。在这一层，我们主要是重写在组件和模式中使用的变量。没有这一层，组件和模式仍然可以工作，但是有一个通用的外观。

[![Screenshot of a rendered output with marked areas of the previously described structure](../Images/f5c745f426ebe0ba46d075147e31bd87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--phLLvjPj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWQ-iOJZbzsVSF3D_qGDiIA.png) 

<figcaption>查看不同路段的行动。</figcaption>

最后但同样重要的是，我们的模式库中有一个所谓的实用程序部分。这主要包含共享 JavaScript 交互的例子，比如展开和折叠元素。它还包含用于辅助功能的 CSS 类。

一旦我们像这样设置好了，我们就能够构建我们的组件——忽略一些额外的编译任务，比如 TypeScript 和 PostCSS。这里就不赘述了。基本上，我们可以通过添加一个 Twig 模板文件、一个 CSS 文件、一个用于文档的 markdown 文件和一个用于示例数据的 JSON 文件来构建我们的组件。**这提供了一个漂亮且易于使用的 HTML 文档，可以与设计师和其他开发人员共享**。

## **向合作伙伴和客户交付模式库**

Pattern Lab 生成一个 HTML 输出，可以用来研究和测试组件。好的方面是它生成原始的 HTML。因此，**整个文档可以压缩并发送给其他人，而不需要设置服务器来使用它**。在我们的例子中，我们仍然在一个测试环境中托管模式库的构建版本，同时在每个版本的 ZIP 中交付文档。

这也是这个项目中的一个重要部分。记住，我们很早就决定使用 Twig。这将帮助后端开发人员直接使用这些组件。幸运的是，这最终非常成功。然后我们交付给后端开发人员的是:

*   我们源代码中的原始树枝文件
*   经过处理和精简的 CSS 和 JavaScript
*   模式实验室构建文档为 ZIP 文件

请注意，在我们使用 Pattern Lab 的所有其他情况下，我们使用 Mustache 构建组件，并且只交付文档和已处理的资产。这是因为我们通常假设开发人员应用了文档中给出的标记和 CSS 类。因此，这个项目是一个例外。

[![Screenshot of one of our rendered pattern libraries](../Images/8601e68ade28f033004cd211756645ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VYH-u1M1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiN57031_SPNlX6bqTGpjeg.png) 

<figcaption>模式实验室:一个活生生的文档。</figcaption>

## **维护:花样库第一**

设计系统和模式库中最大的错误可能是它们没有被维护。这也发生在我们自己的一些模式库上。然而，这在野外相当普遍。

事实上，**模式库给游戏带来了另一个桥梁**。以前，你必须在设计和代码之间架起一座桥梁。一旦你使用了模式库，你必须通过模式库连接设计、模式库和应用设计的最终代码库。这也适用于最知名的设计系统，如材料设计。如果谷歌改变了规则，而你想严格遵守，你必须手动应用设计系统的改变。

例如， [Airbnb](https://airbnb.com) 通过让开发的设计系统成为真实的单一来源来确保他们代码的健康。设计中任何改变的起点都是代码库。他们开发了一些工具，允许你[将代码导入设计软件](https://airbnb.design/painting-with-code/)，使得设计文件的维护变得多余。

不幸的是，**我们还没有到那一步。我们所能做的最好的事情就是确保最终的软件不会有不包含在模式库中的 UI 变化。这很困难，尤其是当一切都必须快速进行并且必须解决一个 bug 的时候。但这是持久一致设计的要求。**

[![Screenshot of Visual Studio Code showing the big folder and file structure of a pattern library](../Images/c02f653f9a22b11feef1e1eb884c4549.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AKwjbFB1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ag2khilHUGeLX_rDB4A2JKQ.png) 

## **结论**

为构建一个模式库投资一个来回值得吗？如果你努力让你的设计在更长的时间内保持一致和可维护，肯定会。模式库为您提供了保持设计概览的工具。无论您的最终应用程序在未来经历什么样的技术发展，它都能让一切保持可持续发展。它将 UI 和逻辑进一步分离开来。这产生了积极的副作用，您可以让更有能力设计隔离环境的开发人员去玩，而不必钻研框架和其他技术。

如果你想看看我们的工作，你可以看看我们当前的样板文件，我们用它来启动我们在 GitHub 上的项目。

## **延伸阅读**

如果你有兴趣了解更多关于设计系统和模式库的信息，请点击以下链接:

*   [Smashing Magazine 上的设计系统类别](https://www.smashingmagazine.com/category/design-systems/)
*   [砸书《设计系统》](https://www.smashingmagazine.com/design-systems-book/)

* * *