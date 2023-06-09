# 为鞋带建立 CI/CD 管道

> 原文：<https://dev.to/dvwbr/building-a-cicd-pipeline-for-shoelace-4de7>

# AWS 豆茎和一个 CD 管道

在过去的几个月里，在鞋带公司，我们已经慢慢地将云托管基础设施从数字海洋转移到亚马逊网络服务。虽然 DigitalOcean 长期以来为我们提供了很好的服务，但我们需要一种功能更丰富的服务来简化自动化部署和负载平衡。虽然有很多知名公司提供云托管服务，但鉴于我们在 S3 等其他地区的服务经验，以及我过去使用该平台的经验，亚马逊是我们最容易的选择。

# 问题空间

我们在 Digital Ocean 上托管了相当规模的服务器(droplets ),每个服务器都签出了我们最新的产品代码，并在 PM2 管理的 NodeJS 实例中运行。这意味着，每当我们想要将新版本部署到生产环境中时，我们都必须手动将 SSH 整合到 six(！idspnonenote)中。)不同的服务器，从 Git 拉最新的，然后通过 Grunt 重新构建，通过 PM2 重新加载节点。虽然这并不是一件困难或难以管理的工作，但是当我们想要每天进行多次生产时，这肯定会变得乏味和浪费。我们不想被陈旧的部署流程所拖累，所以我们需要更好的东西。

# 目标

首先，我们希望减少部署所需的工作量。我们希望轻松、快速、安全地消除部署过程中的任何障碍。实现这一点将使我们能够更快地迭代，给我们带来巨大的灵活性和对市场趋势、API 变化和错误解决的响应。

我之前有一些 AWS 的经验，这影响了我们作为一家公司迁移到该平台的决定(另外，鉴于我们作为一家初创公司的地位，我们可以获得一些免费积分)。知道了 AWS Beanstalk 提供的开箱即用的灵活性，我们还想以最小的成本添加负载平衡——这是 Digital Ocean 当时不支持的。我们还可以根据 AWS 自动扩展策略的流量来扩展和缩小我们的实例。因此，有了许多现成的好处，我们决定继续前进。

# 接近

我首先把我们所有的项目归档。Docker 在生产中让我们安心，因为每个环境中的版本都是相同的。因为我们的栈是建立在 NodeJS 上的，所以我使用了来自 [Docker Hub](https://hub.docker.com/_/node/) 上的 Node 的基本映像。我们的 Docker 映像非常简单，因为它们的主要工作是在创建映像时拍摄代码库的快照。我们使用 Docker run 来执行`npm install`、`grunt build`，然后我们使用 PM2 Docker 作为我们的默认命令。

在那里，我手动设置 AWS Beanstalk 环境。我使用默认触发器启用了负载平衡和自动伸缩，并使用 NAT 网关通过一个 IP 地址过滤出站流量，以简化我们与脸书 API 的交互，该 API 使用白名单来验证请求。在我们的负载均衡器下，不必在每次新的 EC2 实例启动时更新白名单，NAT 网关确保单个 IP 地址向脸书发送请求。我在我们的 EC2 实例中选择了不可变的部署，以消除用户与部分展开的部署交互的机会。

接下来，我编写了一个定制的部署脚本，将连续的部署流程结合在一起。该脚本有几个主要组成部分。

首先，它根据当前目录构建 Docker 映像，并将该映像推送到 AWS 弹性容器注册中心。我也可以在这里使用 Docker Hub，但是考虑到我们的云基础设施的其余部分已经在 Amazon 上，将所有东西放在 Amazon 的保护伞下似乎更简单。

接下来，创建一个 ZIP 文件来存储 Dockerrun.aws.json 文件，Beanstalk 使用该文件在弹性 Beanstalk Docker 环境中配置应用程序，还创建一些低级 Beanstalk 配置文件来确保我们的网络配置在环境更新时得到维护。Dockerrun 文件是 Amazon 以后如何知道要拉哪个 Docker 图像，以便更新我们的环境。这个 ZIP 文件被推送到 S3。

然后，基于我们刚刚推送到 S3 的 ZIP 文件创建一个新的 Beanstalk 版本，然后我们使用新创建的应用程序版本触发一个环境更新。

为了将这一切联系在一起，并给出我们的持续集成和测试部分，我们与 [CircleCI](https://circleci.com) 进行了集成。一旦我们的项目被添加，我就更新我们的 CircleCI 配置，以便在 Github 上的生产分支成功构建后执行我们的 AWS 部署脚本(re:我们的 linter 和自动化测试通过)。

就是这样！现在，每当我们在 Github 上删除一个生产分支时，我们的代码都会被自动测试并部署到 AWS 上。不再需要手动部署。