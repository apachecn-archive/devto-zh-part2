# ASP.NET 静态站点生成

> 原文：<https://dev.to/tonytalkstech/aspnet-static-site-generation-23lk>

正如在[前一篇文章](https://dev.to/tonytalkstech/getting-my-aws-cost-to-nearly-nothing-c78)中所暗示的，我已经从在云托管服务中托管动态 web 应用程序(如 [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/) 或 [Azure App Service](https://azure.microsoft.com/en-us/services/app-service/) )过渡到在我选择的文档存储位置托管静态网站(本例中为[亚马逊 S3](https://aws.amazon.com/s3/) )。这篇文章讨论了我对这样一个项目的开发过程。

# 现有项目

我有很多项目目前都在使用这种部署模式。他们都是一些 ASP.NET 风味的本地开发与本地数据库，然后出口和上传到 S3。

## MorrisPhotos.com

我的摄影网站，[MorrisPhotos.com](https://morrisphotos.com)，是一个位于 S3 的静态网站，通过[亚马逊 CloudFront](https://aws.amazon.com/cloudfront/) 被推到了边缘。我让 [CloudFlare](https://cloudflare.com) 处理 DNS 和威胁检测，一切都进展得非常顺利。

## LodiCornFest5k.com

一个本地的比赛网站，[LodiCornFest5k.com](https://lodicornfest5k.com)，也是一个位于 S3 的静态网站。这个人面前没有 CloudFront，因为在网站上主动缓存数据不太重要。

我的骄傲和快乐，[OhioTrackStats.com](https://ohiotrackstats.com)，是一个位于 S3 的静态网站。以后会有关于这个的博文。

* * *

# 流程

## 局部发展

我是一名. NET 开发人员。这意味着我最喜欢抛出一些 ASP.NET 代码，尤其是对于本地开发。作为。NET Core 2.1 是事实上的标准，我所有的新项目都在朝这个方向发展。

一个简单的例子是我的[MorrisPhotos.com 来源](https://github.com/afmorris/MorrisPhotos)。也许有一天我会更详细地讲述我是如何编码的，但这不是今天这篇文章的目的。

我的本地设置非常简单:我已经让 [Visual Studio 2017 社区](https://visualstudio.microsoft.com/vs/)在我家插着 [ReSharper](http://www.jetbrains.com/resharper/) 的机器上运行。这里没有什么特别的设置。你可以用 [VS 代码](https://code.visualstudio.com/)来代替，但是我喜欢全功能的 IDE。

关于本地开发，我正在添加新特性，修复任何问题，或者只是在我的家用机器上本地运行应用程序，看看它是如何工作的。这里没什么特别的，但这是应用程序真正动态的最后一个地方。

## 本地数据库

我在家里的机器上运行了 SQL Server 2017 Express。它没有什么特别的，但是它有驱动上面列出的每个项目的数据库。为了访问代码中的数据库，我通常使用[实体框架核心](https://docs.microsoft.com/en-us/ef/core/)或[service stack or lite](https://servicestack.net/ormlite)。我对数据库访问层没有任何速度或性能要求，所以我基本上是在编写糟糕的代码来访问数据。

## S3 设置

为了发布一个使用 S3 的网站，我遵循了这个指南。它非常简单明了，我建议您仔细阅读每一步以确保正确。

举个例子，目前我在 OhioTrackStats.com 网站的 S3 有一个`www.ohiotrackstats.com`桶和一个`ohiotrackstats.com`桶。`www`桶直接重定向到非`www`桶。这是我每个项目的模式。

## 域设置

如前所述，我在 CloudFlare 的基础架构中安装了 DNS。对于 MorrisPhotos.com，这看起来像下面这样:

*   通过谷歌域名注册的域名
*   名称服务器设置为 CloudFlare 的名称服务器
*   CloudFlare 的几张 CNAME 唱片
    *   `www`非 morrisphotos.com-> S3 斗主机名
    *   [www.morrisphotos.com](http://www.morrisphotos.com)->T0】S3 斗主机名

## 静态站点生成

这是真正的奇迹发生的地方。给定一个动态的 web 应用程序，我想创建一个静态的 HTML 网站，可以直接通过 S3 托管。

我个人使用 [HTTrack 网站复制器](https://www.httrack.com/)抓取本地运行的 ASP.NET web 应用程序，并将 HTML、CSS 和 JavaScript 文件生成到一个文件夹结构中。这个工具的美妙之处在于，它会将网站上的所有链接更新为相对路径，允许从任何有 web 服务器的地方运行网站。

我确保排除所有外部 CSS 和 JS 文件(例如，托管在外部 cdn 上的文件，如引导文件)，因为我不想在我的网站上托管它们的内容。除此之外，设置非常简单，一旦我建立了一个网站，我就再也不需要更新它了。

为了正确执行这一步，我需要确保我正在本地运行 web 应用程序，然后我指向运行 IIS Express 的工具端口。我尽量确保在发布模式下运行应用程序，以便获得捆绑和缩小的内置好处。

## 部署

一旦生成了静态站点，我要做的就是将输出复制到非`www` S3 桶。我可以通过 [AWS 控制台](https://console.aws.amazon.com)做到这一点，但我不想每次做小更新时都上传整个网站。

为了对抗完全上传，我使用了 [AWS CLI](https://aws.amazon.com/cli/) 。具体来说，我使用一个批处理文件，该文件运行静态站点生成器，然后运行 S3 同步 CLI 命令，只推送已更改的项目。下面是一个示例批处理脚本。我以后再描述。

```
"C:\Program Files\WinHTTrack\httrack.exe" "http://localhost:59141" -O "C:\My Web Sites\LodiCornFest5k" --update aws s3 sync "C:\My Web Sites\LodiCornFest5k\localhost_59141" s3://lodicornfest5k.com --acl="public-read" 
```

Enter fullscreen mode Exit fullscreen mode

批处理脚本中的第一行是从命令行运行 HTTrack 软件，指向`http://localhost:59141`，这是我在 Visual Studio 中为我的 LodiCornFest5k 项目设置的 IIS Express 网站。`-O`开关将应用程序指向镜像位置，这是我已经在 GUI 中用我想要的选项设置好的。最后，我们使用`-update`开关来确保它只写自我们上次运行该工具以来已经改变的文件。

批处理脚本中的第二行是上传行。它运行`aws s3 sync`命令，该命令已经在我的机器上配置为使用一个 IAM 用户，该用户对我的所有 S3 存储桶具有写访问权限。这并不是**最好的**安全性，因为我可能应该将我的用户限制在我需要的范围内，但是现在已经足够了。然后，我将它指向主机上文件的位置(`C:\My Web Sites\LodiCornFest5k\localhost_59141`)，然后指向将文件推送到的 bucket 位置(在本例中为`s3://lodicornfest5k.com`)。此外，我给上传的文件添加了`--acl="public-read"`标志，以确保它们可以通过互联网查看而无需认证，因为我的网站是公开的。

同样，因为我在这个批处理文件中生成静态站点，所以我需要确保我在本地运行网站。我已经多次在没有这样做的情况下将这个批处理文件删除，整个过程非常困难。

## 未来增强功能

有一个主要的改进是我想做的，但是我还没有尝试过。当我进行更改时，不要在本地手动运行批处理文件，我希望 CI 服务器能够代替它。这需要我将`httrack.exe`二进制文件与我的应用程序打包在一起。然后，在提交到`master`分支时，我会让类似 [AppVeyor](https://www.appveyor.com/) 这样的持续集成工具运行一个类似于批处理文件本身的后期构建命令。这将确保 S3 的网站直接匹配源代码控制中的内容，这是圣杯。

我可能会在未来一年左右的时间里抽出时间来做这件事，所以请继续关注！

* * *

# 回顾

总的来说，构建我的定制 web 应用程序的“工作”阶段并没有因为这种新的托管模型而发生显著的变化。每当我做出更改时，我仍然在本地运行和测试。不过，我并没有将构建好的部分推送到 web 应用服务器，而是将生成的文件推送到服务器。对于我和最终用户来说，这是一个更优雅、更快捷的解决方案！

## 节约成本

以前，我使用托管应用程序平台，比如 MorrisPhotos.com(特别是弹性豆茎)。这意味着我在一个 EC2 实例、一个 RDS 实例上产生了费用，加上一些网络和硬件成本。仅托管和运行 web 应用程序每月就要花费 100 美元以上。

现在，仅用 S3 桶托管静态网站(和在单独的 S3 桶中的数千张照片)，加上 CloudFront 作为 S3 桶前面的 CDN 层，我每月支付不到 10 美元。目前我没有确切的数字，但我们预计每月大约可以节省 90%。我要说这绝对值得。

## 节省时间

我没有最终用户实际节省时间的确切数字，但我有一个大致的理解，不是 web 应用程序服务器(在本例中是 IIS)接收请求，交给 web 应用程序处理，交给数据库检索数据，然后将数据推回管道给最终用户，而是简单地给用户一个通常缓存的 HTML/JS/CSS/image 文件。最终用户看到的速度明显更快，在第一个字节的时间方面(通常是由于 MorrisPhotos.com 的 CloudFront 的边缘位置)和总下载时间方面都是如此。

总而言之，这种新的部署和托管范例极大地改善了每个人对我的“动态”web 应用程序的体验，对于需要数据库驱动但只是定期更新的 web 应用程序，这肯定是我从现在开始要做的事情。