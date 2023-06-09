# 在 AWS Lambda 上部署 Next.js

> 原文：<https://dev.to/slotlocker/deploying-nextjs-on-aws-lambda-4f9b>

[归属](https://www.belong.co)是一家愿景简单的公司——“帮助人们发现他们属于哪里”。我们帮助这些人发现公司的一种方式是提供人才品牌和参与解决方案。

这些解决方案中的一些涉及到建立大约 4-5 页的微型网站。由于 Belong 迎合了许多客户，我们很快意识到，我们最终将拥有许多这样的微型网站，它们在技术上有许多共同点。因此，我们开始讨论如何建立一个有效的开发和部署管道。

作为从事这些项目的开发人员，我们希望利用一个堆栈来检查以下内容:

1.  **代码可重用性(DRY)** :这些站点有多个可以共享的 UI 组件/功能。因此，我们决定在 React 中建立站点，因为我们可以利用我们已经建立的组件库！([你可以在这里借阅图书馆](https://github.com/belongco/belong-ui)！) .

2.  服务器端渲染:我们的微型网站通常是静态和动态页面的良好组合。这些网站中的每一个都需要为搜索引擎爬虫进行[优化。因此，我们需要在服务器端呈现 React。](https://developers.google.com/search/docs/data-types/job-posting)

3.  **伟大的发展经历:**对我们来说，以下是伟大的经历:

    I .易于在本地机器上设置

    二。热模块更换(HMR)

    在评估了多种工具之后，我们发现 [Next.js](https://nextjs.org/) 是我们用例最有希望的框架。它非常容易设置，还支持热模块更换。*

4.  **隔离&可靠性:**鉴于这些网站代表了我们客户的品牌，我们需要一个高度可靠的设置。没有人想在停机/性能下降时使用凌晨 2 点的寻呼机。此外，一个客户端站点中的错误不应该影响其他客户端。

5.  **可维护性:**每个客户端都有两个站点实例，即暂存和生产。因此，需要一个可扩展的流程来维护这些站点，而不必单独重新配置每台服务器。

6.  **低费用:**需要优化近 2*(客户数量)个实例的成本。根据人才品牌和招聘需求，每个客户都有不同的工作量。需要一种避免手动调整每个客户端实例的设置。

*无服务器架构检查所有这些点，因此我们决定在 [AWS Lambda](https://aws.amazon.com/lambda/) 上部署我们的 Next.js 应用。它提供了相当大的可靠性，同时保持低成本。*

# 知识库结构

我们有一个单一的存储库来维护所有的微型网站，每个客户有一个单独的分支机构。

主分支充当微型站点的模板。当一个新客户注册时，一个新的分支从 master 派生出来，并对 serverless.yml 文件进行必要的更改(例如:映射到 API 网关端点的域名)。特定于客户的资产也被登记到该分支中。

无服务器配置接受一系列环境变量和参数，这些变量和参数有助于控制部署哪些资源。

# 部署是什么样的？

[Serverless](https://serverless.com/) 使用 Cloudformation 在幕后调出配置文件中定义的资源( [serverless.yml](https://github.com/belongco/nextjs-serverless-setup/blob/master/serverless.sample.yml) )。尽管它主要支持 Lambda 函数及其触发器的定义，但也可以使用原生 Cloudformation 模板来调用其他资源，如 DynamoDB 表等。下图突出显示了我们的部署过程，

[![Deployment Process](img/ee8acb760d4b18f241e02c5fa781f532.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--5ofybj0G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3752/1%2AX7CvR-tzo0ohT9LR7o_2xQ.png) *部署流程*

当一个新的分支/现有的分支被推送时， [Strider](https://github.com/Strider-CD/strider) ，我们的 CI 系统会拾取它并

*   克隆存储库

*   安装软件包依赖项

*   运行棉绒机

*   构建 Next.js 项目

*   将静态文件导出到 S3，带有 S3 URL 的 STATIC_PATH 在 Lambda 环境中设置，供应用程序使用

*   根据环境使用正确的参数部署无服务器配置。

这大大降低了部署过多微型站点带来的复杂性，同时也降低了成本。考虑一个场景，我们必须为 4 个客户部署微型站点，同时保持隔离。传统上，我们将不得不启动 8 个实例，这将花费我们 160 美元以上(假设我们选择最小的 t2.small 实例类型，没有使用 Kubernetes 之类的硬件抽象)。

在无服务器的情况下，考虑到大多数微型网站处理的请求数量非常少，成本加起来只有 10 美元！这是一笔巨大的成本节约，随着微型站点数量的增加，其规模将令人印象深刻。

*注意:在您计划每天处理一百万或更多请求的场景中，将应用程序部署在传统的服务器/容器上比使用无服务器架构更经济。*

虽然现在的设置可以无缝运行，但实现这一目标的途径并不那么简单。当我们开始尝试在 AWS Lambda 上设置 Next.js 应用程序时，我们偶然发现了一系列静态文件服务方面的挑战。

回想起来，我们不建议通过 Lambda 提供静态文件，原因有二:

*   增加了成本，因为我们要为每个请求付费

*   有时，我们可能需要提供大文件(视频、gif ),这些文件无法在 Lambda 的[限制](https://docs.aws.amazon.com/lambda/latest/dg/limits.html)下优化工作。

这就是为什么我们必须在 CI 管道中包含将静态文件推送到 S3 的步骤，作为部署过程的一部分，并通过 Lambda 环境变量使路径对应用程序可用。

如果你仍然想通过 Lambda 提供静态文件(因为你是一个叛逆者😉)，确保在 API 网关配置中将正确的 mime 类型列入白名单。

**Sidenote** :如果你想让你的无服务器部署连接到你在 VPC 内运行的服务，那么你必须[配置](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html#vpc-configuring)Lambda 在一个有 NAT 网关/实例连接的子网内运行。

# 本地测试无服务器

在大多数情况下，您不必使用 Lambda 来检查应用程序是否按预期工作。无服务器框架提供了一个很好的方法来本地测试功能，这有助于节省大量的时间和精力。

# DIY

除了这些知识，我们还希望在 Github 上分享一个简单的初学者工具包，它可以帮助您评估这个架构/设置！

Github 链接:[https://github.com/belongco/nextjs-serverless-setup](https://github.com/belongco/nextjs-serverless-setup)

初学者工具包将:

1.  设置λ函数

2.  为要使用的应用程序配置 DynamoDB 表

3.  设置 API 网关，将其端点映射到自定义域，并为其创建 Route53 条目(使用一些插件进行域映射)

# 前方挑战:

我们的应用用例还要求我们运行一些长时间运行的 crons，Lambda 对执行时间有内在的限制，这使得它不适合运行超过 300 秒的任务。
我们正在评估 [AWS Fargate](https://aws.amazon.com/fargate/) 和 [AWS Batch](https://aws.amazon.com/batch/) 来让这些任务运行起来。希望我们能在另一篇博文中讨论相同的实现细节。

# 关于我们:

**[维韦克](https://twitter.com/kaarthek)卡瑟克**

我是一名前端工程师，与 Belong 的产品团队一起工作。我喜欢用 JS 构建 web 应用程序，然后后悔为什么用 JS 构建它们😅！如果不编码，你会发现我玩国际足联⚽️，阅读历史📖或者学漫画🎨！！

**[法尼](https://twitter.com/slotlocker)T3】**

我喜欢把事情复杂化。💥

👩‍🎨插图作者:Anukriti Vijayavargia

注:本帖最初发布于此处的媒体[。](https://medium.com/@vkaarthek/deploying-next-js-on-aws-lambda-2b8e8ab23697)