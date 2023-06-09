# 沙发底座精简版。网络管道

> 原文：<https://dev.to/borrrden/the-couchbase-lite-net-pipeline-4kal>

作为 Couchbase Lite .NET 的开发人员，我为自己成功建立的管道感到相当自豪。自动化程度越高，你的生活就越轻松，我也在寻找扩展这一点的方法，所以如果有人有任何建议，请告诉我。我也在考虑 Gerrit，但是对于一个开发人员来说，正常的流程似乎太冗长了。不过，如果有提交验证就更好了！希望有人能从我做的设置中拿走一些东西！

这是一个高层次的首尾流程图:

[![Pipeline Diagram](img/ce390bf507c4ca088e211c918b70c2d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HnRFBfel--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/co8fvqp1z5ejy17hvgd7.png)

那么，就从大家都知道的开始吧。我刚刚对回购做出了承诺

[![GitHub commit](img/1c95723f00f45962bb0dbb90015d5da2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u1k03Ef6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p31t8163bupc1pj84rgf.png)

我们的 Couchbase Lite 官方版本。NET 基于包含所需回购和分支以及在哪里检查它们的清单。这个清单格式来自谷歌工具[回购](https://source.android.com/setup/develop/repo)。我们有一个[脚本](https://github.com/couchbase/build/blob/master/scripts/jenkins/manifest/create-build-manifest)，它可以检测清单中密钥回购的变化，如果有变化，就会触发新的源抓取。它作为 Jenkins 作业运行(所有其他下游作业也是如此)。source scrape 将把正确提交时需要的所有存储库拉下来，并把它们全部压缩到一个 GZipped tarball 中进行归档(类似于 GitHub 的“下载 zip”功能，除了它可以跨多个 repos 工作)。然后，该源被上传到 NAS(网络访问共享)机器上，并在内部 HTTP 服务器上可用。

[![Server listing](img/0750132a471a03d60afbda465706a590.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WeCfIU3h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xda2youyfalndjlkbzk7.png)

所以这个提交导致了版本 2.1.0 的内部版本号 129 的源代码抓取。然后，该源抓取作业触发下游作业的构建。构建作业将提取新创建的源代码，并在其上运行构建脚本以生成 Nuget 包。企业版和社区版各做一次。这些包然后被上传到我们的内部 feed，由一个 [Proget 服务器安装](https://inedo.com/proget)托管。

[![Proget package](img/bc0b09c3c60bed4aaf66b416a5546ae0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UPmJwBSR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p40wzr88knlbt9rne8ee.png)

如果这个建筑是幸运的，这正是将被运送的。对大会没有任何进一步的改动。然而，这一进程远未结束。此时我将包上传到 nuget 提要的原因是因为我想检测任何打包错误，并确保发布的包是可用的。因此，包装本身本质上也在接受测试。

上传完成后，将触发下一个下游作业。这是单元测试工作。术语“单元测试”在这里使用得非常松散，因为我没有任何制作实际单元的标准。它只是一个我想确保有效的东西(不知道怎么称呼它)和在被报道之前失败的东西的集合。测试正在进行。NET 核心视窗。NET Core macOS，。NET Core Ubuntu，UWP，iOS 模拟器，Android 模拟器。每个套件运行两次:第一次是从源代码构建 community edition 的调试版本，并在其上运行测试。调试版本有许多额外的断言，可以更快地检测故障。如果这些通过了，那么就可以使用刚刚插入的 feed 中的包进行单元测试的发布构建。开着。NET Core Windows 只做了一次额外的运行来收集代码覆盖率数据。

如果通过，那么所有的包都会被提升到一个单独的 feed。这个提要是为通过第一轮自动化测试的包保留的。我们的 QE 团队可以确信从这个 feed 中提取的包是健康的。接下来触发下一步，为构建 QE 测试服务器应用程序。NET Core、UWP、Xamarin Android 和 Xamarin iOS。测试服务器应用程序是我们使用的一个内部工具，它将通过 HTTP 接受命令，这样它就可以参与到独立流程和程序的编排中。这样做的原因是为了下一步。

流水线过程的最后一步是一组功能测试。这意味着我们需要启动一个(或几个)运行 Couchbase Lite 的程序，还需要编排 Sync Gateway 和 Couchbase Server 的实例，并将它们连接起来相互运行，以测试复制场景。典型的运行如下所示(由一个 python 客户端在一台独立于以下机器的机器上运行 pytest 来执行):

*   安装 Couchbase 服务器(机器 1 /一次性)
*   安装同步网关(机器 2 /一次性)
*   确保同步网关和 Couchbase 服务器已关闭
*   启动 Couchbase 服务器
*   清除 Couchbase 服务器存储桶数据
*   (可选)预填充 Couchbase 服务器存储桶数据
*   使用指向 Couchbase 服务器存储桶的配置启动同步网关
*   下载并安装测试服务器应用程序(机器 3)
*   启动测试服务器应用程序
*   确认所有设备都在正确的端口上监听
*   开始向两个设置场景发出命令，并确认正确的结果
*   停止测试服务器应用程序

这是非常密集的，即使是简单的场景，每次测试也可能需要几分钟(相对于单元测试的几毫秒)。此后，将生成并保存一份关于通过/失败结果以及出错原因的报告。

当需要向我们的预发布 feed 发布一个 DB(开发人员构建)时，几乎就像将一个包从我们的内部 feed 移动到我们的外部 feed 一样简单。中间有一个步骤，解压包，更改 nuget 版本，然后重新解压它，以便它在 Nuget 提要上显示正确的标识符(例如 2.1.0-db001)。当 GA 时间到来时，除了由 QE 团队手动启动的其他测试之外，过程是相同的，除了不是将其移动到预发布提要，而是将其移动到 nuget.org，并且其版本被更改为非预发布版本(例如 2.1.0)。

自动化是非常好的，节省了大量的时间，并帮助您更快地抓住事情。我希望将来我能变得更加自动化！