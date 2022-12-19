# 如何在 App Center 上使用私有 NuGet 服务器(feed)

> 原文：<https://dev.to/muak_x/how-to-use-a-private-nuget-server-feed-on-app-center--1alf>

我想使私人 NuGet 软件包被引用，我调查了它。我把结果写在这里，以防我忘记。

## 储存库

[https://github.com/muak/AppCenterPrivateNugetSample](https://github.com/muak/AppCenterPrivateNugetSample)

整个存储库并不太关心这篇文章，但是我希望这篇文章能帮助你如何编写 Nuget.config。

## 程序

1.  通过一些手段，安排一个服务器，以便把你的私人 Nuget 包。
    *   购买 MyGet 许可证。
    *   使用 VSTS 私人饲料。
    *   利用 VPS 或者云服务，自己搭建一个服务器。
2.  将 Nuget.config 文件放在与. sln 相同的文件夹中。
3.  在应用中心设置环境变量。

## 1。Nuget 服务器

可以跳过这一部分。

首先，我尝试使用我的 Get。但是我放弃了，因为在我的 Get 中使用私有 feed 是不免费的。

其次，我尝试用 nuget.service 包构建一个 nuget 服务器。但我放弃了，因为它不符合 ASP.NET 核心。

然后，我用谷歌搜索“nuget linux”并点击了以下网站。

[https://github.com/Daniel15/simple-nuget-server](https://github.com/Daniel15/simple-nuget-server)

我找到了与此相对应的码头工人图像。

[https://github . com/rolfwesses/docker-simple-nu set 服务器](https://github.com/rolfwessels/docker-simple-nuget-server)

我试着用它。

但是效果不好。所以我分叉这些项目，并做了试验和错误。
结果是以下链接。
虽然我注册了 docker hub，并且可以使用，但这只是一个建议，因为我几乎不了解 docker 知识。

[https://hub . docker . com/r/kamu/docker-simple-nu set 服务器/](https://hub.docker.com/r/kamu/docker-simple-nuget-server/)

[https://github . com/muak/simple-nu set 服务器](https://github.com/muak/simple-nuget-server)
[https://github . com/muak/docker-simple-nu set 服务器](https://github.com/muak/docker-simple-nuget-server)

## 2。Nuget.config

在与相同的文件夹中创建一个 Nuget.config 文件。sln 并编写如下代码。

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="nuget" value="https://www.nuget.org/api/v2" />
    <add key="MyNuget" value="https://mynuget.com" />
  </packageSources>
  <activePackageSource>
    <add key="All" value="(Aggregate source)" />
  </activePackageSource>
  <packageSourceCredentials>
    <MyNuget>
      <add key="Username" value="%private_nuget_user%" />
      <add key="ClearTextPassword" value="%private_nuget_pass%" />
    </MyNuget>
  </packageSourceCredentials>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

[https://intercom . help/app center/build/how-to-restore-a-private-nu get-feed](https://intercom.help/appcenter/build/how-to-restore-a-private-nuget-feed)

虽然我是从这里引用的，但是我把默认的 feed URL 改成了 VS 的 Mac 默认 URL，因为出于某种原因它不起作用了。

将私有 nuget 服务器键值(名称/ URL)添加到 packageSources，并将与添加的键相同的名称项添加到 packageSourceCredentials。并在那里添加用户和密码项。%之间的部分是在 AppCenter 中设置的环境变量。更不用说，它们也可以直接写在那里。

## 3。App Center 上的设置

您所要做的就是在 App Center 上注册与 Nuget.config 对应的环境变量。

[![](../Images/b0f6b5d9ca861cda44091532d546df70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--01XcvlRT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f4rhurduadk70rl81lh4.png)

如果构建成功，下面的日志将很快开始输出。

[![](../Images/b32fa37690da643635065bd76a28aae0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p5wnbTPS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qld3qdt1jywz9e4d55ou.png)

但是，如果任何设置是错误的，将显示以下错误消息，这将需要很长时间来建立。

"无法加载源…
的服务索引"在源…"上找不到包"某物"

如果显示了这些消息，您应该取消构建，因为构建将会失败。