# 开发者中心——简化文档

> 原文：<https://dev.to/ryanhaber/developer-hub---documentation-made-easy-2ai2>

*TL；dr*:Developer Hub([www . Developer Hub . io](https://www.developerhub.io))是一款新产品，可以在几分钟内轻松创建文档。它是干净的，具有核心特征，并且朝着正确的方向前进。

# 产品:

Developer Hub 是一个新的文档产品，它的明确目标是让您快速、轻松地编写和发布文档。它成功了。进入网站后几秒钟内，你就可以创建即时可用的文档。无需构建、转换或模板化您的文档，这是 SaaS 出版最简单的方式。

[![simple wysiwyg editor](../Images/1ca5db1ad8f7862478bfd973ac4ca6ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DPnQYPQw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/ryanhaber/productbrief/content/images/developerhub-wysiwyg.png)

开发者中心有一个简单直观的所见即所得编辑器，这意味着任何人都可以使用它。添加图像或代码块就像点击鼠标一样简单。控制面板简单且相当直观。因此，从现有版本创建新版本这样的任务也很容易。

您可以获得基本的白色标签选项:您可以定制配色方案、徽标和 favicon。显然，这种定制对于一些企业来说是不够的。对于我们大多数人来说，这已经足够了。

输出简洁明了。

[![clean and simple output](../Images/b03a0dbd6f7f58892ee98c5de9a4dc1b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kjSgT1X0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/ryanhaber/productbrief/content/images/developerhub-output-page.png)

可以玩玩我做的小迷你文档演示: [API 大使演示文档](https://api-ambassador.developerhub.io/v1.1/api-ambassador/getting-started)。

# 竞赛:

开发者中心是一个交钥匙 SaaS 文档门户网站，用于小规模的文档项目。正如代码块特性和它的名字所表明的那样，这个产品显然着眼于包含代码样本的产品。它可以很容易地用于其他小文档项目。其他此类产品和服务包括:

[ProProfs 知识库](https://www.proprofs.com/knowledgebase/)

[点击帮助](https://clickhelp.com/)

[吸烟者](https://paligo.net/)

这些产品都是 SaaS 文件的门户网站。

我将 Developer Hub 及其竞争对手与诸如 [Adobe RoboHelp](https://www.adobe.com/products/robohelp.html) 、 [Madcap Flare](https://www.madcapsoftware.com/products/flare/) 和 [OxygenXML 编辑器](https://www.oxygenxml.com/doc/versions/20.1/ug-editor/)等产品区分开来。主要区别在于平台、规模和最终产品。开发者中心在 SaaS。这些产品是企业安装。Developer Hub 在较小的文档项目的整个生命周期中为其提供帮助。这些产品可以处理多个项目中成千上万的文档，对于较小的项目来说，它们是多余的，开销太大。开发者中心提供一个实时网站作为输出。这些产品支持多种导出和输出路径。

帕利戈在某些方面是最接近的。它的用户界面可能与 Developer Hub 最相似。然而，实际上，Paligo 是一个 XML 内容创作工具，可能更类似于 OxygenXML。它的主要价值是单一来源多渠道创作。

开发者中心在这一点上可能不应该进入多通道输出。它主要面向开发人员，因此他们可以坚持使用 web，真正钻研简单的 web 文档创作。它的团队可以通过添加一些单一来源的魔法来真正投入这场战斗。

# 提议:

1.  让格式化控件更明显一点:可能是我。格式控件对我来说并不明显，但它们确实存在。一旦你弄明白或猜测，它们确实有意义，所以我不确定更好的东西是否真的可能。只是团队需要考虑的事情。

2.  代码片段和变量便于单一来源编写:像编码人员一样，编写人员通常希望重用代码块。通常，他们希望能够在一个地方编辑该块，并在其他实例中镜像编辑内容。我们通常称这些块为“片段”。变量允许用户插入在构建或显示时更新的值，如当前年份。片段和变量都是保持内容新鲜和一致的常用方法。无论是片段还是变量，团队都需要注意如何应用样式:无论是从源、引用位置还是某种级联方法。

3.  内部引用:如果页面的 URL 或名称发生变化，文档中的超链接能够自动更新，那就太好了。

4.  Swagger 集成:这里的梦想是为产品内置的 OpenAPI/Swagger 文档提供一个别致的可配置 UI。API 引用可以是一个独立的页面，也可以插入到现有的文档中。直接编辑页面的 HTML 的能力将为这种增强提供一种短期的或者不那么梦幻的方法。

5.  额外的定制:一些客户想要一个他们可以完全控制的登陆页面。一般来说，允许任何特定内容页面的 HTML 视图也是有用的，尽管可能是在现有的模板中。作者可能需要这个视图，这样他们就可以编写表格、设置内部锚点等。注入 css 和 javascript 的能力可能对很多用户也有帮助。

# 将其货币化:

1.  我建议使用免费的基本层，产品随着客户的需求和支付能力而扩展。

2.  我建议 DeveloperHub 团队负责:额外的用户、额外的定制、额外的插件、批量导入/导出或者可能是数据级的导入/导出。

3.  另一个需要考虑提供和收费的服务:一个支持 CSS/JS 的可嵌入 HTML 元素，它对文档进行快速的自动完成/建议完成查询。内容开发人员可以在客户支持请求页面等地方嵌入这样的元素。

# 缩放它:

1.  技术写作商展、协会和论坛似乎是一个非常明显的发布信息的论坛。