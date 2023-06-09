# Azure vs GCP 第 3 部分:比较作为 Linux 平台的 Web 应用和应用引擎

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-3-compare-web-apps-and-app-engine-as-linux-platform-a43>

到目前为止，我只是在 Azure 和 GCP 上部署了 dotnet core web 应用，没有考虑 underline OS。因此，很自然地，我将我的应用程序部署到 Azure 的 Windows 托管环境中。在本文中，我使用 Linux 上的 Web 应用程序来看看它们有什么不同。

# [T1。净支持](#net-support)

由于是 Linux，所以只支持 dotnet 内核。我使用与本系列第 1 部分中使用的相同的应用程序。

# 部署到 Linux 上的 Azure Web Apps

当您将 dotnet 2.0 应用程序部署到 Web 应用程序时，有两种选择。

*   使用 docker 容器
*   使用 dotnet 2.0 运行时

让我们看看 docker 容器部署是如何工作的。顺便说一下，我不能成功地部署到 dotnet 2.0 运行时环境，但我会解释步骤。

#### Windows Docker

Windows 现在支持 docker，但是你需要安装它。
[Docker for Windows](https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows) 。您需要支持虚拟化的硬件。不过，我相信现在大多数电脑都是这样。

## 部署为 docker 容器

1.要使用 docker，您需要启用它。这可以在项目创建时完成，或者像本文一样稍后完成。右键单击项目|添加| Docker 支持。
[![docker](img/22ee3711faac193a78505c8b843a7bf3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--8zvvMJn7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qzamsot4zfy6emr8fpm0.PNG)

2.选择“Linux”作为目标操作系统。
[![docker](img/2dd1b74d53f2f90baa3780932b9ec04f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--_IukHhb2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wj70uf4bmfxqpuur5m6q.PNG)

3.现在这个解决方案有了 docker-compose 项目。右键单击项目，然后单击“发布”。单击“创建新配置文件”。
[![publish](img/ef4ac759a6201e0f1db0623785021c9b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--WL_oJrRb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/us34vey7l0s9xmyt6z90.PNG)

4.Visual Studio 2017 检测 docker 支持，给我们 Linux(甚至容器注册表)选项。选择“微软 Azure App Service Linux”，点击“发布”。
[![publish](img/7d57232481ef808a7daed274cad798a6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--z4EY61Y2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lbw0h9e6wvv4ffaj1f0u.PNG)

5.创建新的资源组和应用服务计划。这里有一个警告。

*   Linux 上的 Web 应用程序需要适用于 Linux 的应用程序服务计划，这需要单独的资源组。
*   Linux 平台没有“免费”计划。

因此，我在这里指定了新的资源组和“S1”应用服务计划。请记下应用服务计划名称，以便稍后使用。
[![publish](img/74fccbc9f2c12b380c3c0dccca64f20e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--MbMdx1w7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yt85npei6b643o6pnhgx.PNG)

6.那就等着吧。这需要一点时间，所以如果你想喝咖啡的话。同时，Visual Studio 在 Azure 中创建资源，将映像推送到注册表，并部署它。
[![publish](img/9f12143661bd123fd796e854ad7cec55.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--meIKvV-2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9rlzm7lubacoryav5mty.PNG)

7.部署完成后，访问站点 URL。
[![publish](img/7f5b45f73b878e61222edeea650db976.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Kzg39XhI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fwnnyt66y8cpb7xdjngq.PNG)

## 部署到 dotnet core 2.0 运行时

Web Apps 还提供了 dotnet core 2.0 运行时环境。它的工作原理与 Windows 环境非常相似。但是 Visual Studio 不支持 docker 版本那样的直推。

让我们通过重用上面创建的资源组来创建新的 Web 应用程序。我使用嵌入在门户中的 CLI 功能。

1.进入 [Azure 门户](https://portal.azure.com)，点击顶部菜单栏上的“云壳”图标。
T3![portal](img/81cba2018bc4f422f72430d2a1d2202b.png)T5】

2.运行以下命令在 Linux 上创建新的 Web 应用程序。将上面创建的计划名称替换为您。Web 应用程序名称必须是全球唯一的。'- deployment-local-git '参数告诉 Web 应用程序我使用 git push 来部署。

```
rgname=CloudCompareLinuxRG
planname=WebApplication120180302090521Plan
webappname=dotnetcoreonlinux123
az webapp create -g $rgname -p $planname --name $webappname --runtime "dotnetcore|2.0" --deployment-local-git 
```

Enter fullscreen mode Exit fullscreen mode

3.复制显示在最后一个命令之后的 git url。地址如下所示。
[https://108 bones @ dotnetcoreonlinux 123 . SCM . azure websites . net/dotnetcoreonlinux 123 . git](https://108bones@dotnetcoreonlinux123.scm.azurewebsites.net/dotnetcoreonlinux123.git)

4.运行以下命令创建部署用户。用户名需要是全局唯一的。

```
az webapp deployment user set --user-name <username> --password <password> 
```

Enter fullscreen mode Exit fullscreen mode

5.如果您还没有初始化 git，右键单击 solution 并选择“将解决方案添加到源代码管理”。
[![git](img/5fda35e2e4f4a14627c7b914115738a7.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ElffTBgr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qo90882axxy9elfb1i2r.PNG)

6.转到“团队资源管理器”并单击“同步”。
[![git](img/30a214809e29258bb219abab69883e1c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--hdWi4nyb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8qg5u7207efhnhgi2iu7.PNG)

7.选择“发布 Git Repo”。
[![git](img/06f313b92168eccf388914b7eea7327f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cq06eS6m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7kksu3q1d080v5kuy07t.PNG)

8.输入您在上一步中获得的 git 地址，然后单击“发布”。
[![git](img/28b82a918fc209fab59bf7e49b726095.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--1MqNdYrX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7te3ar7o41qt4h94cost.PNG)

9.输入您在上一步中设置的用户名和密码，并等待推送完成。初推需要时间。
[![git](img/dbbe7c1fd4105654e4ebee0c18bd927b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--K5y9ono---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7r6d5bxrax0ezpuovfis.PNG)

嗯，这应该可以，但是我遇到了一个问题，我无法使用这种方法成功部署。我可能错过了什么或临时的问题？无论如何，这是我得到的错误。
[![git](img/06987cfc94cedc84336f06256748da70.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--utDhHUaf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cptt3bq1sy8y48oz2t0l.PNG)

正如我们可以通过 Git 部署一样，我们也可以通过 Visual Studio 2017 或 shell 轻松部署。

# GCP App 引擎灵活

我已经在第 1 部分的[中将应用程序部署到 App Engine。我错过了什么吗？当然了。在这篇文章中，我只是补充一个信息。](https://dev.to/kenakamu/azure-vs-gcp-part-1-aspnet-developer-experience-22i)

## 定制 App 引擎灵活环境。

正如在[第 1 部分](https://dev.to/kenakamu/azure-vs-gcp-part-1-aspnet-developer-experience-22i)中测试的那样，部署到灵活的应用引擎非常简单。但是如果你想定制环境，你可以这样做。

1.由于我在上一步中添加了 docker 支持，已经把项目搞砸了，所以让我们从头开始重新创建新的应用程序。我创建了“WebApplication2”项目。
[![app](img/55d073ee3087298a442491fd64d70bce.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ppe-O11T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vo1zwn2dfzfmyp9aqcep.PNG)

2.右键项目，选择“生成 app.yaml 和 Dockerfile”。

[![publish](img/5c117ca5f159cb8d46601e70367e7484.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UNDQeiM_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qac3n5oxeocruqua5l3r.PNG)

3.创建 app.yaml 和 DockerFile。然后，您可以修改文件并发布。例如，您可以指定需要多少个实例以及每个实例的资源。

```
runtime: aspnetcore
env: flex

manual_scaling:
  instances: 3
resources:
  cpu: 2
  memory_gb: 2.3
  disk_size_gb: 10 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

有趣的是，即使在同一个平台上，体验也会有所不同。我强烈地感觉到，作为一名开发人员，我不能回避容器技术。

# 参考文献

[Linux 上的 Azure App 服务简介](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro)
[将 ASP.NET 容器部署到远程 Docker 主机](https://docs.microsoft.com/en-us/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)
[用 app.yaml 配置你的应用](https://cloud.google.com/appengine/docs/flexible/dotnet/configuring-your-app-with-app-yaml)

肯恩(男名)