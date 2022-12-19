# Qlik Core 入门

> 原文：<https://dev.to/qlikbranch/getting-started-with-qlik-core-2781>

几个月前，Qlik 发布了 Qlik Core T1，这是 Qlik 关联数据引擎的一个文档化版本。在玩了几周 Core 之后(开发者版本是免费的…另外，我在 Qlik 工作)，我开始意识到这个产品在可视化和分析数据方面有多棒。所以我写这篇博客来解释，**以我的拙见**，谁将从 Core 中受益，以及如何开始使用它。

### 首先，为什么要 Qlik Core？

如今，有几个可视化库， [D3](https://d3js.org/) 可能是其中使用最广泛的库。[点击这里](http://bibviz.com/)查看构建在 D3 之上的交互式可视化的完整示例。对于这样的应用程序，静态数据是与应用程序一起打包和加载的。但是这种方法有一些限制，特别是当数据很大时(例如，大于 200 MB)…

1.  这个应用程序需要花一生的时间来加载。
2.  浏览器占用大量内存，如果数据大小超过限制，甚至会导致浏览器崩溃。
3.  处理过滤和关联并不容易。
4.  在浏览和筛选数据时，很难管理状态。
5.  处理动态数据很困难。

一种替代方法是查询数据库，在进行过程中请求和接收数据，但这也有局限性。主要缺点是用户可能会经历查询延迟。通常，基于查询的系统需要 0.5-3 秒来响应用户操作，这降低了用户体验和交互式仪表板的目的。😟

### Qlik Core 前来救援——但是什么是*Qlik Core 呢？*

 *[![Paw Patrol Picture](img/c6d4e094e91bc6a43cb7a15e6fff3bf4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rg21vyui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ANlcaOoOfeficrIKVBnsLEg.jpeg) 
*不相干的爪子巡视图片(我是新家长)，从[到这里](https://www.hollywoodreporter.com/live-feed/nickelodeon-renews-paw-patrol-3-more-preschool-series-1113681)。*

> Qlik Core 让您可以直接访问首个同类数据引擎。而“数据引擎”既不是数据仓库，也不是数据库。这是一种处理数据关联、分析和状态的机制，尤其是在大量数据中。

当您在应用程序的🧡拥有这个数据引擎时，您不需要发送查询来接收数据。即使从不同来源提取数据，Qlik Core 也可以将数据加载到内存中，对其进行处理和索引，然后被动地驱动 web 应用程序。数据成为内存中的实例，您的 web 应用程序现在通过 WebSocket 连接到 Qlik Core，所有计算和关联都是远程处理的。

**所以，如前所述，使用 Qlik Core 有很多优势，尤其是在数据量很大的情况下……**

1.  应用程序数据驻留在服务器中，而不是传递到前端。从查看者的角度来看，这不仅增加了可用性，而且还减少了服务器的负载，使得前端和后端的性能都显著提高。

2.  开发人员不需要担心通过他们的代码连接过滤和关联。所有这些以及状态管理都由 Qlik Core now 处理，应用程序的可视化和交互性可以直接挂接到引擎中。

3.  最重要的是，Qlik Core 非常擅长从不同的数据源加载数据，并将它们整合到一个数据源中。

所以，我们像他们说的那样，一石多鸟。现在你知道我为什么认为这个产品很棒了吧(😍→这是我用 Qlik Core 构建的，我发誓这不仅仅是因为我在 Qlik 工作。

### 准备好入门了吗？

[![ready to get started gif?](img/9d96b69762a33a40d2309225930412dd.png)](https://i.giphy.com/media/xT9IgzUuC5Ss6ZnTEs/giphy.gif) 
*满怀希望，你就这样点头。*

Qlik Core 相当新，因此入门可能有些棘手(我们正在努力改进，但同时，请给我们反馈)。Qlik Core 的官方文档位于[https://core.qlik.com/](https://core.qlik.com/)。在那里你会找到[这个教程](https://core.qlik.com/get-started/)，我觉得它非常简单。遵循这些说明将使您的 Qlik 核心实例快速运行，尤其是如果您熟悉 Docker 的话。还有一个由 Qlik Branch 制作的教程[视频](https://www.youtube.com/watch?v=KSa6nL6goOs)(更多内容将会推出，但这是我们所有开发者和 Qlik 的家)介绍了基本知识。

但这之后是什么呢？

### 让我用一些简单的例子给你看看…

我构建了一些示例来说明如何使用 Qlik Core。基本上，要使用 Qlik Core，您需要[enigma . js](https://help.qlik.com/en-US/sense-developer/September2018/Subsystems/APIs/Content/Sense_ClientAPIs/enigmajs/enigmajs-introduction.htm)(Qlik 的开源库之一)来使用 JavaScript 轻松地与引擎通信。与 Qlik Core 对话的唯一方式是通过我们的[引擎 API](https://help.qlik.com/en-us/sense-developer/2.1/Subsystems/EngineAPI/Content/introducing-engine-API.htm) ，它是一个语言无关的、基于 WebSocket 的 API。Enigma.js 只是一个包装器库。

因此，这里有一些例子，以便您可以更好地了解 Qlik 核心如何工作…

*️⃣ [你好发动机示例](http://jsfiddle.net/wuzhong/7fmru3kd/)

*️⃣ [你好数据示例](http://jsfiddle.net/wuzhong/1vg47myc/)

*️⃣ [检查核心实例是否健康](https://q-core-healthcheck.herokuapp.com/)

*️⃣ [在应用程序中查看数据模型](https://q-model-viewer.herokuapp.com/)

*️⃣ [上传一个预建的应用到 Qlik 核心](http://ec2-54-255-168-158.ap-southeast-1.compute.amazonaws.com:8080/)

希望这足以让您开始了解和使用 Qlik Core。随着时间的推移，我们将添加更多的教程、提示和技巧，以帮助开发人员不仅开始使用 Qlik Core，而且开始使用 API 本身。

所以请关注 Qlik 分支的[知识板块。与此同时，请查看那里的教程，它们解释了更多关于我们的关联数据引擎、它是如何工作的以及如何使用它。如果你*真的*对 Qlik Core 感兴趣或者需要帮助，请告诉我。只需在下方留言或加入我们的](https://developer.qlik.com/knowledge) [Qlik 分支 Slack](https://medium.com/r/?url=http%3A%2F%2Fdeveloper.qlik.com%2Fslack) (见#qlik-core 频道或 DM me [at]wu_zh)。

干杯，编码快乐，万圣节快乐。

—吴

[![Halloween Joke Picture](img/d8d95874e9bf77d4b18adf084881a6e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k9knXjIU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AAXlDRbKCE-FReIA-iG0ckQ.jpeg)*