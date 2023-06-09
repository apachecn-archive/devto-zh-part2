# 为. NET 库创建 CI/CD 管道:第 3 部分——构建在 AppVeyor 和 Travis CI 之上

> 原文：<https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-3---building-on-appveyor-and-travis-ci-4ai2>

# [T1】简介](#intro)

在上一篇文章中，我们使用 [Cake](https://cakebuild.net/) 定义了构建，包括将结果工件发布到 [NuGet](https://www.nuget.org/) 和[连体工作服](https://coveralls.io/)(尽管我们将在后面看到连体工作服的更多细节)。在这篇文章中，我们将使用 [AppVeyor](https://www.appveyor.com/) 和 [Travis CI](https://travis-ci.org/) ，它们为开源项目提供免费的持续集成托管。

# 将项目添加到应用供应商

AppVeyor 是与一起使用的主要 CI 云提供商之一。NET 项目，尤其是在多平台之前。NET Core 的到来，但仍然非常有用，如果不是为了别的什么，建立在 Windows 上。

## 在 AppVeyor 站点设置项目

创建账户后，添加项目真的很简单。

在主页上，创建新项目:

[![AppVeyor Home](img/1604e5d52684f5dfd00974116706fc73.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/m8oget7rncrfepk8yslv.jpg)

然后选择一个来源。在我的例子 GitHub 中，确保授权 AppVeyor 访问 GitHub 库:

[![AppVeyor New Project](img/24df070443279e7d4c26930c75bd8ce3.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/v1kz985g3osr4j3smd19.jpg)

下一个屏幕将是空的，在我的例子中已经有一些关于以前构建的信息(包括日志):

[![AppVeyor Latest Build](img/3d6050f5e1551cfd45feb6258725c359.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/hmhwgla1nrx5645xs2ww.jpg)

下一步是进入设置部分并设置好一切，但我没有做任何更改，因为我正在使用 Cake 构建脚本和项目存储库中的一个`appveyor.yml`配置文件来配置一切。

## 使用 appveyor.yml 进行配置

就像上一节提到的，构建的配置可以在 AppVeyor 网站上完成，但是使用`appveyor.yml`文件我们可以做同样的事情，并且将所有东西都放在源代码控制中。

考虑到大部分工作都是在 Cake 中完成的，AppVeyor 的配置非常简单——我只需在这里插入它，然后浏览它。