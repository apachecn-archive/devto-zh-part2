# 为. NET 库创建 CI/CD 管道:第 4 部分——工作服、徽章和总结的代码覆盖率

> 原文：<https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-4---code-coverage-on-coveralls-badges-and-wrap-up-2bmp>

# [T1】简介](#intro)

在之前的帖子中，我经历了一个相当简单的. NET 库 CI/CD 管道所需的大部分工作，从使用 [Cake](https://cakebuild.net/) 定义构建，使用 [AppVeyor](https://www.appveyor.com/) 和 [Travis CI](https://travis-ci.org/) 在云中运行构建。

这最后一篇文章会很小，因为我只会做一些收尾工作，比如使用[套装](https://coveralls.io/)使代码覆盖报告在线可用，以及用构建状态信息装饰项目的 GitHub 页面。

# 将项目添加到工作服中

既然构建工作在云 CI 提供者上，我们可以添加测试代码覆盖率的美妙触感，不仅可以看到百分比，还可以浏览代码，看看覆盖了什么，没有覆盖什么——这可能比一个松散的百分比数字更相关。

还有更多的选择——我早就想到了吗，我可能会使用 SonarQube 不仅进行覆盖率分析，还进行一些静态代码分析，我稍后会再讨论它——但是我在这个例子中使用了工作服，没有特别的原因，只是为了尝试一下。

鉴于覆盖率报告的生成和发布已经在 Cake 的帖子中讨论过了，我们现在要做的就是在工作服网站上创建项目。

在主页中，我们可以添加一个新的存储库。我的主页已经有一个项目的参考，因为我已经添加了它。

[![Coveralls Home](img/4ee698febd65e753f0c4666fa9c1f84b.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/e8liazjgz1ye5iv4whdj.jpg)

添加一个库真的就像打开/关闭开关一样简单，假设我已经注册了我的 GitHub 帐户，我所有的项目都已经可以添加了。

[![Coveralls New Project](img/a36bc2b566664adea99bbb19e89b7115.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/76vjycxqts41h8oe2a6a.jpg)

当我们发布结果时，它们会显示在特定项目的页面上。

[![Coveralls Project Page](img/c276bedd2c08b667acdcbd8c0b1d3eca.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/pdnghkbd3tm6hmyvo3c0.jpg)

然后我们可以深入到项目的文件中来查看覆盖率。

[![Coveralls Project Files Drill Down](img/10a77894c38e8b0c17ff5a4bdedbc72f.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/mk6a8dr57dg8bz56vojk.jpg)

然后单击一个特定的文件并查看它的覆盖范围细节。

[![Coveralls File Details](img/854fc4f46d8c0b54b536ff7477919d2e.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/7c5w4wee5gzi6qylr281.jpg)

由于我未能彻底测试异常，我们有工作服工作😀

# 奖励:CI 状态徽章

现在一切都正常了，我们不妨通过显示 CI 状态徽章，让它在 GitHub 项目页面中可见。

[![GitHub Badges](img/e576f0c5148d929fd760b38c1da23a37.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/5dtrico5cdlq8sgv20kf.jpg)

对于 AppVeyor 的徽章，在项目设置中有一个“徽章”部分，其中有您可能需要的详细信息，以及主分支的示例。

[![AppVeyor Badges](img/26a5035cc5dc96bb6fd1fd40a6d8f254.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/2uib94ns8soaoqljhu1t.jpg)

对于 Travis CI，我们可以复制显示在项目页面顶部的状态图像的图像地址(在他们的文档中没有找到这方面的具体部分，如果有，请评论)。

[![Travis CI Badges](img/efa059a87ee35b9ed5beb7e2949d44ec.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/4gxuo95qycg85uwriyqs.jpg)

对于工作服，在项目页面上也有一个有用的嵌入选项的徽章。

[![Coveralls Badges](img/ce216c366818f908b00843827c392fe2.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/6049f7yjspsnvbxby4jd.jpg)

现在，我们只需要在 markdown 中放上几张桌子，上面有所有服务的徽章，我们就可以开始了。

# 包装完毕

因此，总结所有这些(post series wise)，最终的结果是当我做出更改并点击`git push`时，代码在不同的环境中被构建和测试，覆盖率被发布，最终库也被发布，除了推送代码(并合并到 master 以实际发布包)之外没有任何手工工作。

我们也看到了使用 Cake 来定义 C#中的构建是多么的实际。开箱即用的东西工作得很好，如果有什么东西不见了，我们可以直接编码！我很确定还有很多可以改进的地方，所以如果你对解决方案或帖子有任何反馈，请发过来！

*   [第 1 部分-简介](https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-1---intro-2j61)
*   [第 2 部分-用 Cake 定义构建并发布到 NuGet](https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-2---defining-the-build-with-cake-and-publishing-to-nuget-4ph3)
*   [第 3 部分-建立在 AppVeyor 和 Travis CI 的基础上](https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-3---building-on-appveyor-and-travis-ci-4ai2)
*   第 4 部分-代码覆盖工作服，徽章和总结(此职位)

这些帖子的附带代码是[这里的](https://github.com/CodingMilitia/GrpcExtensions/tree/july-blog-post)(标记是为了确保代码反映未来这里写的内容)。

PS:原贴[此处](https://blog.codingmilitia.com/2018/07/30/creating-ci-cd-pipeline-dotnet-library-part-04-coverage-coveralls-badges-wrap-up)。