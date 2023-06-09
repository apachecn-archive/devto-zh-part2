# 为. NET 库创建 CI/CD 管道:第 1 部分-简介

> 原文：<https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-1---intro-2j61>

在这一系列的文章中，我想谈谈为作为 NuGet 包发布的. NET 库创建 CI/CD(持续集成/持续交付)管道。作为参考，我将使用我在之前的[帖子](https://dev.to/joaofbantunes/dotnetifying-grpc-sane-edition-3d4k)中提到的 [GrpcExtensions](https://github.com/CodingMilitia/GrpcExtensions) 库。

鉴于我们处于这个跨平台的新世界。NET，我想不仅仅在 Windows 上构建和测试东西，所以我使用了 [AppVeyor](https://www.appveyor.com/) 和 [Travis CI](https://travis-ci.org/) ，前者处理 Windows 上的构建，后者处理 Linux 和 MacOS 的工作。

考虑到 CI/CD 管道使用多个提供者， [Cake](https://cakebuild.net/) 有助于以提供者和系统不可知的方式定义所需的任务。我们可以使用 C#来定义构建任务，这也很酷，这很容易理解，因为项目的其余部分已经是 C#了。

最后，为了让测试代码覆盖率可见，我使用了[工作服](https://coveralls.io/)来保存这些信息。

添加一些静态代码分析也是不错的，比如[sonar cube](https://www.sonarqube.org/)，但是还没有这样做，这可能是另一篇文章的好主题。

**免责声明** :
我假设读者从我提供的非常小的介绍中理解了我在说什么，但如果我假设错了(这是很有可能的)，请随意提问，我会回答并最终改进这些帖子。

用一系列的帖子代替一个大的，这样(希望)更容易阅读。下面是便于浏览的帖子列表。

*   第 1 部分-简介(这篇文章)
*   [第 2 部分-用 Cake 定义构建并发布到 NuGet](https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-2---defining-the-build-with-cake-and-publishing-to-nuget-4ph3)
*   [第 3 部分-建立在 AppVeyor 和 Travis CI 的基础上](https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-3---building-on-appveyor-and-travis-ci-4ai2)
*   [第 4 部分-工作服、徽章和包裹上的代码覆盖范围](https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-4---code-coverage-on-coveralls-badges-and-wrap-up-2bmp)

这些帖子的附带代码是[这里的](https://github.com/CodingMilitia/GrpcExtensions/tree/july-blog-post)(标记是为了确保代码反映未来这里写的内容)。

PS:原贴[此处](https://blog.codingmilitia.com/2018/07/30/creating-ci-cd-pipeline-dotnet-library-part-01-intro)。