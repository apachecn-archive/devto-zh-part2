# 静态分析工具如何帮助我生产更好的软件:理解设计

> 原文：<https://dev.to/elemarjr/how-static-analysis-tools-are-helping-me-to-produce-better-software-understanding-the-design-2461>

[![](img/db8f78d0b2f793f51abe68a547e23312.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BU8iOsXV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/05/helloquence-61189-unsplash-300x200.jpg)

作为一名顾问，我经常需要处理我不知道的代码。我需要理解不属于我的设计决策。我需要熟悉我没有帮助构思的建筑。静态分析工具，如 [NDepend](https://www.ndepend.com/) 和 Visual Studio 代码图，帮助我更有效地完成工作。

今天，我将开始一个系列，在这里我将分享静态分析工具是如何帮助我产生更好的软件的。在本帖中，我将与你分享我在接触一些不熟悉的代码库时的第一步。

## 步骤#1:识别组件及其关系

举个例子，让我们假设我需要开始处理微软的 [eShopContainer 项目](https://github.com/dotnet-architecture/eShopOnContainers)。

在下载代码并启动和运行之后，我的第一步是从解决方案中生成一个代码图(使用 Visual Studio)。

[![](img/779bd7cfcfe39ba7cbe4b2b61cd0637d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sucJFQfu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/05/codemap.png)

这个简单的视图让我对软件组件及其关系有了初步的了解。默认情况下，Visual Studio 使用解决方案的文件夹结构对项目进行分组。

请注意，web 应用程序和服务之间没有“明确的依赖关系”。此外，服务之间也没有“显式依赖”。发生这种情况的原因是连接只存在于运行时，通过 HTTP/消息传递(这很好，考虑到这是微服务架构实现)。但它要求我与人交谈，以了解业务。

这种观点也帮助我发现了一些添加到项目中的“技术复杂性”。例如，EventBus 至少有两种实现。这有必要吗？这样做的商业动机是什么？在“真实世界”中，将存在两个“发布”策略，两个支持策略，…(我知道这是一个示例项目，但是，…)

## 第二步:发现每个组件的大小

我的第二步是生成一个依赖图，用盒子大小表示代码行数(使用 NDepend)。

[![](img/9f6d27f377614597650a8ebad537af7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r5PA0NVp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/05/DependencyGraphSnapshot.png)

现在我对项目的比例大小有了一个很好的想法。此外，我可以估计阅读代码所需的时间(这并不精确，但一般来说足够了)。

这个图表帮助我确定了应用程序组件中不成比例的大小。不成比例的大组件是糟糕的架构/设计选择的一个很好的标志。比如有一个“这么大”的身份工程，就很奇怪。还有，奇怪的是，订购 API 比订购域和订购基础设施突出两倍。API 会做得比它应该做的更多吗？

在“现实世界”中，我已经看到“大”组件是满足业务需求的耦合和困难的征兆。

## 第三步:发现核心(难以改变的)组件

我的第三步是生成依赖图，这次使用传入耦合来定义盒子大小。

[![](img/e33e3caef03eefe2d00e2f64eb24095b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zFSN_xvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/05/DependencyGraphSnapshot_ac.png)

分析结果图，我知道 EventBus 是这个项目中的核心组件和概念。另外，我知道改变 EventBus 是危险的，因为它会影响整个系统。

同时，在这一刻，我开始思考和询问服务的“编排”。这里似乎没有“指挥者”。我说的对吗？

重要的是要记住，这个图不包括“隐式依赖关系”在分析代码时，没有任何工具能完成你的全部工作。图表、报告、矩阵只会帮助你更加精确，节省(一些)时间。您仍然需要与领域人员交谈。

## 第四步:更好地(详细地)了解关系

我的第四步是生成一个依赖矩阵。

[![](img/68464855ba98b6ea68f290bf20733026.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2wt_xIOn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/05/dependencymatrix.png)

蓝色单元格表示列中的程序集正在使用行中的程序集。绿色单元格表示该行中的程序集使用该列中的程序集。

这个矩阵让我看到所有的网络项目都使用“微软。AspNetCore.HealChecks”包。不错吧。！

在现实世界中，我见过有成千上万个相互依赖的程序集。

## 第五步:创建关键问题列表

最后(有一段时间)，我使用 NDepend 来检查代码中的关键潜在问题。

[![](img/f1bf93f5fb532178328158a9d28a5139.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ECW7YE0x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/05/problems.png)

NDepend 使用通用规则来尝试识别代码中的难点。它通常非常有效。

“避免方法太大…”是最常被违反的规则。

## 最后一句话

在这篇文章中，我建议你在接触不熟悉的代码时使用适当的工具。在下一篇文章中，我将开始分享一些我用来深入挖掘代码发现和消除痛点的技术。

我能帮你吗？让我知道。

帖子[静态分析工具如何帮助我生产更好的软件:理解设计](http://www.elemarjr.com/en/2018/05/7905/)最早出现在 [Elemar JR](http://www.elemarjr.com/en) 。