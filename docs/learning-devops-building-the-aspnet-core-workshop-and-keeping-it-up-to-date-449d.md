# 学习 DevOps，建立 ASP.NET 核心研讨会，并保持其最新状态

> 原文：<https://dev.to/azure/learning-devops-building-the-aspnet-core-workshop-and-keeping-it-up-to-date-449d>

> DevOps 是人员、流程和产品的结合，能够为我们的最终用户持续提供价值。
> 
> 多诺万·布朗

[![Build status](../Images/a8c27fa510bf1070881c11d70039bce3.png)T2】](https://dotnetfoundation.visualstudio.com/AspNetCoreWorkshop/_build/latest?definitionId=30)

你有多少次去 GitHub repo 发现样本代码不是最新的 SDK，或者甚至没有构建？

在过去的几周里，我(再次)更新了团队在各种会议上用于 1 天和 2 天会议的[ASP.NET 核心研讨会](https://github.com/dotnet-presentations/aspnetcore-app-workshop)。网芯 2.1。然而，作为其中的一部分，我们希望确保任何未来的更改都得到检查，而不仅仅是在我的机器上工作。

研讨会有很多，每个都有自己的恢复/保存点，另外，还有一个角度水疗服务选项以及码头选项。

1.  [会议#1](http://tattoocoder.com/docs/1.%20Create%20BackEnd%20API%20project.md) |安装 bits，使用基本 EF 模型构建后端应用程序
2.  [会话#2](http://tattoocoder.com/docs/2.%20Build%20out%20BackEnd%20and%20Refactor.md) |构建后端，提取 EF 模型
3.  [会议#3](http://tattoocoder.com/docs/3.%20Add%20front-end%2C%20render%20agenda%2C%20set%20up%20front-end%20models.md) |添加前端，呈现议程，设置前端模型
4.  [会话#4](http://tattoocoder.com/docs/4.%20Add%20auth%20features.md) |添加验证，添加管理策略，允许编辑会话，用户可以使用 Twitter 和 Google 登录，自定义验证标签助手
5.  [会话#5](http://tattoocoder.com/docs/5.%20Add%20personal%20agenda.md) |添加用户关联和个人议程
6.  [会议#6](http://tattoocoder.com/learning-devops-and-building-the-asp-net-core-workshop-up-to-date/docs/6.%20Deployment.md) |部署、Azure 和其他生产环境、配置环境、诊断|
7.  [第 7 场](http://tattoocoder.com/docs/7.%20Challenges.md) |挑战
8.  [会话#8](http://tattoocoder.com/docs/8.%20SPA%20FrontEnd.md) | SPA 前端

### 更新代码

从 2.0 -> 2.1 迁移并不难，正如[发布时在博客](https://docs.microsoft.com/aspnet/core/migration/20_21?view=aspnetcore-2.1&WT.mc_id=cicd-devto-shboyer)中记录的那样。更重要的是不要错过任何一个*csproj 文件或 NuGet 包等。，并确保每个项目都能做到`dotnet restore`、`dotnet build`。当然，如果这些检查通过，`dotnet publish`在适当的地方，还有码头工人的事情...

### Visual Studio 团队系统

VSTS 太棒了，比我想象的还要棒。我过去在我的开源项目中使用过 Travis 和 Circle CI，我对 Jenkins 很熟悉。看看我需要完成什么，这是工作的正确工具。

#### 建设管道

首先，我只需要使用 GitHub 的 oAuth 连接器从 repo 中获取代码。

[![Learning DevOps and building the ASP.NET Core Workshop and keeping it up to date](../Images/681cfe9bf135a457f11ba2f489da66e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Mp9IOu1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/content/images/2018/06/Screen-Shot-2018-06-25-at-1.19.34-PM.png)

接下来，在每个保存点添加构建应用程序的每个步骤非常容易。我通过为每一个创建一个“[阶段](https://docs.microsoft.com/vsts/pipelines/process/phases?view=vsts&tabs=web&WT.mc_id=cicd-devto-shboyer)来完成这个任务。

这里第一阶段在`/src`文件夹中构建代码，这是完成的 workshop，产生两个[工件](https://docs.microsoft.com/vsts/pipelines/release/artifacts?view=vsts&WT.mc_id=cicd-devto-shboyer)； **backend.zip** (Web API app)和**frontend . zip**(Razor Pages app)

[![Learning DevOps and building the ASP.NET Core Workshop and keeping it up to date](../Images/824376dbd5eeb157a6ce568fadc027ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JEp8ZQ1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/content/images/2018/06/Screen-Shot-2018-06-25-at-1.27.01-PM.png)

构建多个“阶段”,并且当手动或通过触发器(PR 或 merge)启动构建时，它们将并行地、顺序地或两者都进行，这取决于构建代理的可用性和/或阶段之间的依赖性。

此屏幕截图显示了所有已完成的阶段，以及“已完成的研讨会”阶段或工作的每个步骤的摘要。

[![Learning DevOps and building the ASP.NET Core Workshop and keeping it up to date](../Images/76ffacb2b5424226481aed5ecbf5f012.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--444Xc7w8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/content/images/2018/06/Screen-Shot-2018-06-25-at-11.32.17-AM.png)

以下是 Docker 选项的步骤总结。

[![Learning DevOps and building the ASP.NET Core Workshop and keeping it up to date](../Images/57c6990f02ba974c42e92afe611049ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MAToQY2U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/content/images/2018/06/Screen-Shot-2018-06-25-at-11.32.52-AM.png)

单击该阶段中的任何一个步骤，都会显示所发生事件的详细日志。

[![Learning DevOps and building the ASP.NET Core Workshop and keeping it up to date](../Images/446c660746db2b3267c691615819fb3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dGJzRYlX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/content/images/2018/06/Screen-Shot-2018-06-25-at-11.33.22-AM.png)

#### 发布

当构建成功完成时，来自第一阶段 **backend.zip** 和 **frontend.zip** 的工件可以作为发布发生的触发器。在这里，我们已经设置了一个发布，因此每当一个成功的构建完成并且新的工件可用时； **backend.zip** 和 **frontend.zip** 都通过 web deploy 部署到 Azure 应用服务。

[![Learning DevOps and building the ASP.NET Core Workshop and keeping it up to date](../Images/f31dd90615c2cc9127ef4acd44281a17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tT0_ZM_g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/content/images/2018/06/Screen-Shot-2018-06-21-at-4.16.40-PM.png)

### 其他版本

作为构建过程的一部分，在“deployment-docker”构建阶段，我们创建了许多 docker 映像。**前端**、**后端**和一个 **nginx 代理**镜像，它们被推送到 Azure 容器注册中心(ACR)。

Linux 中的一个多容器 Azure 应用服务有一个 webhook，当这些图片更新时，它会得到通知，并把它们拉向这个新服务。

### 定制代理商、码头工人和更多的善良

每个构建管道和/或阶段都可以被配置为具有特定的构建代理。这是托管执行构建的 [VSTS 代理的地方。在 workshop 构建中，我们将其设置为在 Linux 主机上构建。然而，我们可以选择 Windows、Mac OSX 或配置我们自己的代理。](https://docs.microsoft.com/vsts/pipelines/agents/hosted?view=vsts&WT.mc_id=cicd-devto-shboyer#use-a-microsoft-hosted-agent)

### 使用码头工人 VSTS 代理

VSTS 团队发布了许多在 Docker 中运行 vsts 代理的不同选项。[参见 Docker Hub 上的回购](https://hub.docker.com/r/microsoft/vsts-agent/)

您将需要一个来自 Visual Studio Team Services 实例的 [**个人访问令牌**](https://docs.microsoft.com/vsts/pipelines/agents/v2-linux?view=vsts&WT.mc_id=cicd-devto-shboyer#permissions) 。

我使用了`microsoft/vsts-agent:ubuntu-16.04-tfs-2017-u1-docker-17.03.0-ce-standard`图像，因为它包含了生成图像并将其推送到 ACR 所需的 Docker 合成功能。

> 注意，您不能运行“docker 中的 Docker”，所以我们需要通过将 docker sock 从主机绑定到容器来在代理容器中运行 Docker 任务。

```
docker run  
  -e VSTS_ACCOUNT=myinstance-shayne \ƒ

  -e TFS_HOST=myinstance-shayne \
  -e VSTS_TOKEN=xxxxxxxxxxxxxxxxceyeqvu3thm6rxdfjooo3tgquyu6qu26a \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -it microsoft/vsts-agent:ubuntu-16.04-tfs-2017-u1-docker-17.03.0-ce-standard 
```

Enter fullscreen mode Exit fullscreen mode

运行容器会下载 vsts 代理，并将其附加到构建服务器，作为构建的可用代理。用这个做实验很有趣。我在 Mac 上运行了 4，在 Azure Container Service 中启动了一些(这需要一段时间来启动，图像是 3 Gb ),并将它们设置为非 Docker 构建阶段。

[![Learning DevOps and building the ASP.NET Core Workshop and keeping it up to date](../Images/7f56131e8c9563cb5f615f2c7ece6e7a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PGRo5s1e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/content/images/2018/06/Screen-Shot-2018-06-21-at-9.12.09-PM--2-.png)

### 资源

VSTS 入门超级容易；查看文档示例。NET Core - [为构建您的第一个 CI/CD 管道。网络核心](https://docs.microsoft.com/vsts/pipelines/apps/aspnet/build-aspnet-core?view=vsts&tabs=gitvsts%2Cweb%2Cdeploy-windows&WT.mc_id=cicd-devto-shboyer)