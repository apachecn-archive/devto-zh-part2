# 快速构建内部应用程序的 GraphQL

> 原文：<https://dev.to/hasurahq/graphql-for-building-internal-applications-quickly-22p>

[![](../Images/8234decf9fd55c72737c9672fc3d022a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r320ps8H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/graphql-for-building-internal-applications-quickly-29cf37c1de8/1-7x1D_cbtR472y6OPZtmY5Q.jpeg)

*原载于 2018 年 8 月 8 日* [*App 开发者杂志*](https://appdevelopermagazine.com/using-graphql-for-building-internal-apps-quickly/) *。*

信息技术在组织中的角色从单纯的横向职能转变为战略优势，这种转变正在全世界上演。然而，这些努力背后的大多数变更管理技术倾向于不成比例地关注面向最终用户的软件——通常以组织内或内部应用程序(应用程序)为代价。

内部应用推动数据驱动的决策或支持面向终端用户的服务。它们还可以帮助您成为当今消费者期望和竞争要求的快速发展、敏捷的组织。对于企业来说，同等关注内部应用的质量和特性速度是至关重要的。

在这篇文章中，让我们看看 GraphQL 如何帮助企业做到这一点。

## 组织内部应用面临的挑战

内部应用程序的开发流程通常面临以下问题:

预算&人员配备:与面向用户的应用相比，内部应用通常预算不足，人手不足。这个缺点给应用开发团队的**生产力**带来了很高的溢价。

**与现有技术的兼容性**:组织内部的应用程序通常不是绿地项目，因此它们需要与现有系统中的数据进行交互。这需要不同的团队在每次发布时相互协调。拥有这种复杂性和混合的多个涉众通常对特性速度来说不是一个好兆头。

在一个受限的环境中，糟糕的“构建还是购买”决策的冲击会更加明显。因此，与购买现成的(OTS)产品相比，我们应该偏离主题，选择合适的内部构建应用。

应用程序可以根据应用于数据的分析或业务逻辑的复杂性以及它们与底层数据库交互的性质进行分类。以下是根据这一分类做出构建还是购买决策的有用指南:

[![](../Images/e8f3225b617f16eb1b50de4409dab800.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QAiKp3oT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/graphql-for-building-internal-applications-quickly-29cf37c1de8/1-i-wGlsd-b5aKlmf6uB0Yzg.jpeg)

既然我们已经知道要构建什么样的应用程序，那么让我们看看如何将它们构建得更好。但是首先，简单介绍一下 GraphQL。

## 什么是 GraphQL？

GraphQL 是 API 查询语言的规范，也是执行查询的服务器端运行时。开源规范以一个类型化模式为中心，该模式可供 API 用户(主要是前端开发人员)对公开的字段进行任何 CRUD 查询。它也不知道底层数据库或任何其他数据源。

GraphQL 吸引应用程序开发的关键特性是:

更少的错误:类型化模式导致更少的错误，因为常见的错误可以在开发阶段就被发现。

**灵活性**:一旦被支持，一个模式就可以服务于针对它的任何查询排列。举一个简单的博客应用程序的例子，一个包含作者和文章表的模式将在不增加额外成本的情况下满足这两个查询:

*   获取作者列表和每个作者的文章
*   获取文章列表和每篇文章的作者

**统一**:无论数据来源如何，GraphQL 都为标准化的 app 开发工作流程提供了统一的查询接口。

**可扩展性** : GraphQL 还可以用来将多个数据源缝合在一起，以至于它可以在同一个 API 调用中与这些不同的数据源进行交互。

由于这些原因，GraphQL 的采用一直在飞速增长。由于它对 API 生命周期管理的影响，现在它被吹捧为传统 REST APIs 的替代品。

## graph QL 如何帮助内部 app 开发转型？

GraphQL 对应用开发的影响在 API 生命周期管理上相当明显。下面是它如何影响一个 [API 开发周期](https://appdevelopermagazine.com/find-bugs-in-your-code-before-launch-with-new-regrade-platform/)的不同阶段:

更少的 API:graph QL 的模式驱动方法减少了需要开发的 API。如上面的博客应用程序示例所示，与相同模式交互的新 API 不会增加成本。这种灵活性也适用于纯粹需要新 API 来转换服务器端输出格式的情况；GraphQL 规范允许前端应用程序指定输出格式，并查询它们想要的任何数据片段。

**对客户端应用的影响:**使用 GraphQL APIs 的一般前端开发人员体验正迅速成为其他开发工具期望效仿的黄金标准。

*   客户端代码可以从 GraphQL 模式中自动生成。
*   实时数据等用例。，来自实时查询非常容易处理。
*   与 REST APIs 的相同工作相比，前端开发人员在与 GraphQL APIs 集成上花费的时间要少得多。

**自动 API 文档:**通过发布的模式，GraphQL 将 API 发现或文档隐含地构建到服务器中，并带有利用这一点的工具。

[![](../Images/ac23dfed2ea0c9923479da7c97615492.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0vGIbhle--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/graphql-for-building-internal-applications-quickly-29cf37c1de8/1-F3SLus-geWYNa6W0EMGTvA.jpeg)

**测试&部署:**graph QL API 只需要在模式发生变化或者新模式出现时进行测试(*客户端仍然需要测试*)。这也适用于部署。与每一次迭代都是测试和部署的候选的[REST API](https://appdevelopermagazine.com/postman-update-gives-developers-more-freebies/)相比，问答和开发运维的成本要低得多。

由于其类型化模式，GraphQL 还有助于减少运行时错误和相关测试。使用客户端工具/SDK 来利用这一优势并提供编译时客户端查询验证是 GraphQL 生态系统中的规范。

这就是我的图片“IDE 中的查询验证在正式测试之前防止错误”应该放在它下面的标题的地方

[![](../Images/62d20d10840c1082139324196da8cac6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dQ0Ghv1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/graphql-for-building-internal-applications-quickly-29cf37c1de8/1-cxwZ_dLjze-YzCCDjore_Q.jpeg)

**标准化代码和工作流程:**使用 GraphQL 这样的规范，在代码库和流程中实施一定程度的标准化。考虑到 IT 人员的流失率，这是 GraphQL 的一个实实在在的好处。

## 与现有技术的兼容性

让应用程序与现有技术相结合是一个重要的用例。除了利用现有投资，来自这些不同来源的数据，尤其是遗留应用程序/数据库，通常需要一起查询。这几乎总是会带来技术和操作上的噩梦。

GraphQL 非常适合将不同的数据源放在统一的查询接口后面，因为它对底层数据库是不可知的。

这里有一个来自 James Baxley 的轶事，是他在 NewSpring Church 使用 GraphQL 的经历:

*“我们希望使用 Meteor 帐户的系统和反应数据，获得财务帐户，显示捐赠历史，并显示 Rock 的个人资料信息，将我们所有的图像和内容存储在我们的 CMS 中，我们希望使用 Google 的网站搜索 API 进行应用内搜索。利用 GraphQL 的强大功能，一个开发人员就能在一周内将所有的* [*数据库*](https://appdevelopermagazine.com/how-the-reddit-security-breach-reminds-us-to-be-careful/) *和系统连接到一个端点*

将多个数据源缝合在一起通常是一次性的工作，一旦处理好，就不再需要昂贵的跨职能操作。

## 结束语

GraphQL 的效率和交付的自动化工具的范围意味着开发内部应用的团队可以在典型的预算/资源限制下更好地工作，因为对后端开发人员的依赖大大减少了。事实上，通过一些开源工具自动生成现成可用的 GraphQL APIs， [GraphQL](https://appdevelopermagazine.com/graphql-as-a-service-platform-launches-by-hasura/) 使前端开发人员能够过渡到能够自己管理整个应用的全栈开发人员。

像纽约时报、Github、Spotify 等几个知名机构。，已经冒险采用 GraphQL 作为他们的主要 API 技术。生态系统已经成熟，拥有处理大部分繁重工作的工具。例如，Hasura 提供了一个现成的 GraphQL 服务器，它可以从数据库中自动推断模式并管理底层实现。

因此，当务之急是组织评估 GraphQL，如果他们的发现是积极的，就开始绘制组织范围的采用路线图。在内部应用上试用 GraphQL 对任何组织来说都是一个无风险的好机会。