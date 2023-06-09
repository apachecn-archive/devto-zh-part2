# Azure Pipelines -将 CI/CD 从 GitHub 添加到 Azure Functions ⚡

> 原文：<https://dev.to/azure/azure-pipelines-adding-ci-cd-from-github-to-azure-functions-2dh6>

去年发布了 Azure Devops。与 GitHub 的 CI 集成看起来很整洁，所以我想我应该在一些无服务器应用程序中尝试一下。

下面是我如何着手配置一个非常简单的设置！

[![](img/e0fa2c217ba118b36bb270c4931bed0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E9sqdkx5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AN8tgXcKNJ6eHOOzhXExsDQ.png)

先决条件:

*   一个现有的 Azure 功能 App — ( [教程](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-app-portal?WT.mc_id=fnciblog-devto-debryen))
*   GitHub 存储库，其中包含您的函数代码。如果你没有，可以分叉我的[超级简单函数](https://github.com/deanobalino/functions-simple) repo，跟着走。

让我们从 GitHub 市场的 Azure Pipelines 插件开始。

[![](img/3e72f0feed0ca78487dd4bfe6ca50b88.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qVk-qTi8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Az6LyL8U8YFG9_yorn0tfRA.png)

让我们着手制定一个计划。

[![](img/a7fef6dee981a54962bb6a22e5205346.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vt3JmZEd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5EXPH3EFFBMfs8vOsKNUUg.png)

Azure Pipelines 对公共和私人项目都是免费的，公共库甚至可以免费获得 10 个并行作业。因此，除非您的私有存储库需要多个并行作业，我们在本教程中肯定不需要，否则您可以选择 free 计划。

继续安装 marketplace 应用程序，并登录到您的 Azure 帐户。您应该被重定向到 Azure Devops 门户。您现在已经准备好为我们的无服务器应用程序设置 Azure 管道了！😀

首先，选择您的存储库，我将使用我的函数——简单存储库。

[![](img/625df3349e067aee9e0d029ee6545be6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mUqdGOph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AI8afZGM29MVnyIWoOlzGuw.png)

Pipelines 现在将分析 git 存储库并推荐一个初学者模板。这可能会根据您的应用程序而有所不同，您可以在这里随意使用。但我将选择“Starter Template ”,因为我们将放弃它，从头开始编写我们的构建管道。

[![](img/6c1018b8fab99c1705149ce5d721aa62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pGziVU75--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ArinlFzP_rR5EQ5w5QLbLjw.png)

这就是奇迹开始发生的地方。通过创建一个名为`azure-pipelines.yml`的 YAML 文件，将构建管道定义为代码。我们一会儿就写我们的。但是让我们先了解一下为什么这很棒。

*   您将您的`azure-pipelines.yml`文件放在您的存储库的根目录下。
*   在推送链接的 GitHub 库时，Pipelines 将触发构建，这将遵循`azure-pipelines.yml`中概述的步骤和配置
*   因此，您可以将构建管道配置放在 GitHub 中控制的应用程序代码和版本旁边。

那么，我们构建管道的`azure-pipelines.yml`文件看起来像什么呢？

**代理配置**

我们的 YAML 文件的第一部分定义了将运行我们的作业的代理池。这些是为我们运行构建步骤的主机。这里我们决定使用运行 Ubuntu 的托管 Linux 代理。

```
pool:
  vmImage: 'Ubuntu 16.04' 
```

**构建步骤**

接下来，我们需要定义我们的构建步骤。我们的构建将有三个步骤。

1.  运行一个`npm install`
2.  为我们的代码创建一个 zip 存档
3.  发布我们的构建工件，以供我们的发布管道获取

当然，在真实环境中，您可能会运行 mocha 来执行测试，进行一些静态代码分析，或者使用像 [funcpack](https://github.com/Azure/azure-functions-pack) 这样的工具来改进冷启动。所有这些也很容易在管道内实现。但是为了简单起见，我们将坚持这三个步骤。

首先，我们使用`script`步骤运行`npm install`。不言自明。

```
steps:
  - script: |
      npm install
    displayName: "npm install" 
```

其次，我们使用内置的存档任务创建代码的存档。

```
- task: ArchiveFiles@2
    displayName: "Archive files"
    inputs:
      rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
      includeRootFolder: false
      archiveFile: "$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip" 
```

脚本使我们能够在代理主机上运行命令行脚本，任务是来自 Azure Devops 或市场中的第一方的额外功能。这里我们使用`ArchiveFiles`任务来打包我们的代码。我们也可以在这里引用 Azure Devops 提供给我们的一些有用的变量，你可以看到我们正在使用这些变量来标识`$(System.DefaultWorkingDirectory)`和`$(Build.BuildId)`，以定位我们的代码并用一个有意义的名称保存它。

最后，我们添加一个任务来发布我们的构建工件。

```
- task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: '$(System.DefaultWorkingDirectory)'
      name: 'drop' 
```

这里，我们将工件放入名为`drop`的`DefaultWorkingDirectory`中。当我们部署到 Azure Functions 时，我们将从发布渠道中获得这一点。

这就是我们构建管道的全部内容！我们定义构建管道的`azure-pipelines.yml`文件已经完成，最终文件应该是这样的:

```
pool:
  vmImage: 'Ubuntu 16.04'
steps:
  - script: |
      npm install
    displayName: "npm install"
  - task: ArchiveFiles@2
    displayName: "Archive files"
    inputs:
      rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
      includeRootFolder: false
      archiveFile: "$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip"
  - task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: '$(System.DefaultWorkingDirectory)'
      name: 'drop' 
```

您可以在浏览器中删除 starter 模板，并用类似于上面的 YAML 文件替换它。然后点击“保存并运行”。这将提示您将`azure-pipelines.yml`提交到链接的 GitHub 存储库。添加可选的提交描述，然后再次单击“保存并运行”。

## 释放管道

因此，现在我们已经构建了我们的构建管道，并发布了我们的构建工件。我们现在需要创建一个发布管道来将我们的应用程序部署到 Azure 功能中。不幸的是，今天发布管道不支持 YML。因此，我们将在 GUI 中创建它。

首先，单击 Devops 门户中管道标题下的发布，然后单击“新建”按钮。

[![](img/53c498f18f5f925b167a6fdd72e2d8de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5NavX6t1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A2YxuDMammuL4m1iZAbcbZg.png)

我们将使用“Azure 应用服务部署”模板，因此我们将选择该模板，然后点击“应用”。

[![](img/2020bda6afdabf1ab9eeeaa94cdf9eba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iona2jbt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AlETmCaABKu46EdHcfj38qw.png)

如果你愿意，你可以给你的舞台起一个名字。所以我把我的命名为“部署到 Azure 功能”

[![](img/bdf3e7e209c5c66a0ec5295692ff4d4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CHiAiqvD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A532kwsCudR7You0VwWsKew.png)

接下来，我们需要配置我们希望在发布管道中执行的任务，作为这个阶段的一部分。我们将点击 pipeline 中的“1 job，1 task”链接来完成这些设置。

[![](img/e29a184f0266af950bbe9d2d4937af58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5U19xVku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Akk_K7JZgGBDnJU50ZDgcnQ.png)

系统会警告您，我们的应用服务部署任务缺少一些设置。让我们把这些修好。

首先，我们需要授权 Azure Devops 和我们的功能应用程序所在的 Azure 帐户之间的连接。如果您使用与 Azure 相同的帐户登录 Azure Devops，您应该能够从下拉菜单中选择相关的订阅。选择后，点击“授权”。你会看到一个弹出窗口，要求你登录你的 Azure 帐户并授权，在后台，这是创建一个具有贡献者角色的服务主体，允许 Azure Devops 部署到你的订阅中的功能应用程序。

确保您已将应用程序类型设置为“功能应用程序”，然后您应该能够从“应用程序服务名称”下的预填充下拉列表中选择您的功能应用程序。

我们将保留 Package 或 folder 字段的默认值`$(System.DefaultWorkingDirectory)/**/*.zip`，它将从这里获取我们构建管道的已发布工件。

[![](img/dc49427623751846172dff84b941a5ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DkKLjK-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AaUUrfNKIuShhpYgxWB-SDw.png)

回到门户中的“管道”选项卡。接下来，我们需要告诉我们的发布，我们希望它部署哪个构建工件。所以点击“添加工件”。

[![](img/13fd4c2e9a7cc789a2fb1ad013c5677e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mD-xs9OJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A0hpZsdNgBvsBRjMwKe72Ug.png)

这里，我们用构建管道的设置来配置我们的工件。只需使用预填充的下拉菜单完成表单，并选择我们之前创建的构建管道的值。

[![](img/4d45bdd48e9bec7615269540019d5a65.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lMl7N5Sl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AG0hM65QmxqgotLt1wyjDgw.png)

所以现在我们有了一个工件，以及为这个版本执行的一些步骤。我们现在需要设置一个连续部署触发器。这将告诉 Azure Devops 何时运行我们的版本。继续点击我们工件旁边的闪电符号。

[![](img/e811b6d688f7cddfc6f1bdaf3b0e6e19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xIJekdRm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ArM1hemumrruplZEw4ATAkQ.png)

我们可以告诉服务在每次有新的构建可用时创建一个发布，这将是由于我们之前设置的 GitHub CI 集成，我们每次提交到我们的主分支。因此，选中复选框以启用持续部署触发器。

[![](img/07fb020294ab86f13b21957cafe6a07e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_mX9swRi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ABQknfvxlN_drpHOnPlhKcg.png)

## 测试

现在我们有了 GitHub CI 与我们的构建管道的集成，完成后将通过我们的发布管道触发我们对 Azure 功能的持续部署。

为了检查这一点，在你的函数代码中编辑一些东西，提交给 git，然后推送到你链接的 GitHub 库。坐下来，看奇迹发生…

**注意:**如果使用我的函数——简单的例子，你可以简单地改变`/hello/index.js`的响应体。

如果你点击 Builds，你应该会看到在推送到 GitHub 时，一个新的构建已经开始了。单击该特定构建将提供该构建的摘要。一旦完成，你应该有一个大的绿色复选标记！

[![](img/166e0038ed5d927e96d4148ce80242a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YhKRu0x9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AkiHGVlV-0ENJjEh_4TfTBg.png)

如果您想更深入地了解这些步骤，可以单击 Logs 选项卡，查看构建的每个步骤以及每个步骤花费的确切时间，如果您单击其中一个步骤，将会看到底层代理运行该构建步骤时的原始日志。

[![](img/2ee2dac4a6587789a6fb538774fcd0c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--utP-31oV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AePwM2JH2THZvsecoUgkgCg.png)

一旦我们的构建完成，我们的持续部署触发器应该已经启动，我们的发布管道应该已经启动。点击左侧的“发布”,查看我们的渠道进展情况。

[![](img/a3068129001d2d4d46a32751ad03a495.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dmend_aR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AGlv3WJR0jJQtUDafmhtHdg.png)

在这里，您将看到发布管道已经被触发，并且您应该看到我们的阶段“部署到 Azure Functions”突出显示为绿色，并带有一个复选标记，表明已经成功。

现在你可以执行你的 Azure 函数，并看到你所做的改变已经生效。快乐的日子！

## 呜呜

就这样，现在您已经使用 Azure 管道为您的 Azure 功能设置了 CI/CD，直接从推送至您的 GitHub 库😀

如果你想更深入地了解，就去看看 Azure Devops 文档。在这里，您可以在我们所做的基础上构建多种环境、发布关卡、项目管理等等！