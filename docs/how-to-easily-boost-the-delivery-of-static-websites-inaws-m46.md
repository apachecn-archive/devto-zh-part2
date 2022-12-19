# 如何在 AWS 中轻松提升静态网站的交付

> 原文：<https://dev.to/kylegalbraith/how-to-easily-boost-the-delivery-of-static-websites-inaws-m46>

我已经写了很多关于静态网站的用例，以及利用 AWS 来托管、保护和交付它们。这毕竟是我的[通过使用 It 课程](https://medium.com/r/?url=https%3A%2F%2Fwww.kylegalbraith.com%2Flearn-aws)来学习 AWS 的背景，我们使用它来加速我们对 Amazon Web Services 的学习。

除了我的课程，我还整理了一些关于静态网站的博客文章。

*   [如何在 S3 开设网站而不迷失在大海中](https://medium.com/r/?url=https%3A%2F%2Fmedium.freecodecamp.org%2Fhow-to-host-a-website-on-s3-without-getting-lost-in-the-sea-e2b82aa6cd38)
*   [如何使用 CloudFront 安全交付全球静态网站](https://medium.com/r/?url=https%3A%2F%2Fmedium.freecodecamp.org%2Fhow-to-make-use-of-cloudfront-for-secure-delivery-of-static-websites-to-the-world-d2f54e8b096)
*   [通过 AWS 证书管理器向静态网站添加免费 SSL 证书](https://medium.com/r/?url=https%3A%2F%2Fblog.kylegalbraith.com%2F2018%2F03%2F02%2Fadding-free-ssl-certificates-to-static-websites-via-aws-certificate-manager%2F)
*   [如何使用 GitHub 和 AWS 持续部署静态网站](https://medium.com/r/?url=https%3A%2F%2Fmedium.freecodecamp.org%2Fhow-to-continuously-deploy-a-static-website-in-style-using-github-and-aws-3df7ecb58d9c)

这些帖子对于刚刚开始学习 AWS 的人来说非常棒。为什么？因为它们提供了一个环境，使我们能够在实际使用服务时学习各种服务。

今天，我想更详细地介绍一组特定的服务，这些服务可以帮助我们优化静态网站的交付。

特别是，我们将重点关注如何利用 AWS CloudFront、我们的内容交付网络(CDN)和 AWS S3 来优化我们在 AWS 上的静态网站的交付。

这篇文章假设你已经满足了以下先决条件。如果你错过了其中的一个，请随意阅读上面的其他帖子。

*   你有一个静态网站托管了 AWS S3。
*   该静态网站通过 AWS CloudFront 提供服务。
*   为了持续部署您的网站，您的 AWS 帐户中配置了 CI/CD 管道。

你准备好开始了吗？让我们开始吧！

### 对于我们的用例来说，缓存看起来像什么？

在我们开始优化之前，我们首先需要快速了解一下缓存。

简而言之，缓存是一种机制，通过这种机制可以将某些内容存储起来以备将来使用。通常，这种存储比另一种形式的存储更快，也更接近用户。例如，我们可以将数据库查询的结果缓存在本地内存中，这样我们就不需要再次从数据库中读取它们。

让我们考虑一下我们的静态网站基础设施。

[![CloudFront to S3 bucket](../Images/62777d6aa61d1f6e57c96ddbce8e4b91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GX5lx_DI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ty64m22tsao9occrvxyb.png)

我们看到我们的 CloudFront 发行版位于 S3 网站的前端。当一个请求进入我们的分发，它被转发到我们的 S3 网站。如果我们从缓存的角度考虑这个问题，我们的分布比我们的 S3 存储桶更接近终端用户。

如果我们能够将内容存储在 CloudFront 上，并消除对每个请求都使用我们的存储桶的需求，我们就可以加快将资源交付给用户的速度。

幸运的是，CloudFront 支持开箱即用。

在撰写本文时，CloudFront 在全球拥有 124 个边缘位置和 11 个边缘缓存。流入我们的发行版的请求有以下步骤。

1.  该请求被中继到离用户最近的边缘位置。
2.  该位置检查所请求内容的缓存位置。如果缓存中有内容并且没有过期，缓存的内容将返回给用户。否则，请求被传递到源，在那里从源请求内容。

坦率地说，缓存可能很难。这是因为我们冒着无意中向用户提供陈旧数据的风险，因为他们得到的是缓存的响应。但是正如我们将要看到的，通过将 CloudFront 与 AWS 中的 CI/CD 管道相结合，我们可以在几分钟内在我们的发行版中实现基本的缓存。

### 调整 CloudFront 来缓存我们的静态网站

当我们最初配置我们的发行版时，我们保留了默认的缓存规则。

我们将**最小 TTL** 设为 0，**最大 TTL** 设为 31536000(一年)，**默认 TTL** 设为 86400(一天)。

按照我们目前的配置，我们所有的静态网站资源都会在 CloudFront 中缓存一天。这是因为我们没有向 S3 存储桶中的对象添加任何缓存头，并且我们的最短生存时间(TTL)设置为 0。因此，我们的分布依赖于一天的默认 TTL。

目前，每当我们将静态网站部署到 S3 时，我们必须向 CloudFront 发出一个无效命令，告诉它转储缓存的结果，以便将新的请求转发到我们的 S3 存储桶。

这里我们可以优化两件事。首先，让我们将发行版的默认 TTL 从一天改为一个月。

我们可以通过 AWS 控制台编辑我们的默认行为来做到这一点。

1.  在 AWS 控制台中转到 CloudFront。
2.  单击您的发行版的 ID。
3.  选择“行为”选项卡。
4.  检查“默认”行为。
5.  点击“编辑”。
6.  将“默认 TTL”从 0 更改为 2628003。
7.  点击“是，编辑”。

一旦这种变化在我们的发行版中传播开来(这可能需要 5-10 分钟),我们所有的资源将默认缓存在 CloudFront 中一个月。

我们可以在我们的 Chrome 开发工具中观察到这一点。对图像的第一个请求有这样的响应。

[![Miss from CloudFront header](../Images/67e8d629e2408ca028c9d761b275460e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8lcqXQTf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2okrwbfd4atlfc88uex3.png)

注意底部的`x-cache`标题。它说这个请求导致了 CloudFront 的一个失误。这意味着我们的请求被转发到我们的 S3 存储桶，因为响应不在边缘缓存中。

如果我们刷新页面，一个新的请求会被发送到我们的发行版，这次我们会在我们的`x-cache`标题中看到一个*“来自 CloudFront”*。

这是因为该图像的响应现在存在于我们的边缘缓存中，并且将保持一个月，或者直到我们使缓存无效。

现在，让我们探索如何在 AWS 中使用 CI/CD 管道，以便在部署网站时使缓存失效。

### 使用 AWS 代码管道中的 CI/CD 管道使我们的缓存无效

所以我们已经采取措施在 CloudFront 中缓存响应一个月，而不是一天。但是，现在我们有一个陈旧的缓存问题。

如果我们每天甚至每小时都在不断地开发我们的网站并向 S3 发布新内容，那会怎么样呢？对于每次部署，我们必须从命令行运行我们的`create-invalidation`命令。

[![CloudFront create-invalidation command](../Images/cf8c988e2ec55be934bbbe569f92f016.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DXQguvcK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bplavvaau101jzdj9ffq.png)

这听起来既乏味又痛苦。

幸运的是，我们可以利用我们使用 AWS CodePipeline 和 CodeBuild 创建的 [CI/CD 管道来为我们实现自动化。](https://medium.com/r/?url=https%3A%2F%2Fmedium.freecodecamp.org%2Fhow-to-continuously-deploy-a-static-website-in-style-using-github-and-aws-3df7ecb58d9c)

如果您还记得，我们有一个特殊的文件`buildspec.yml`，它定义了 AWS CodeBuild 项目构建、测试和部署静态网站的步骤。这是那个文件现在的样子。

```
version: 0.2
phases:
 install:
   commands:
     - echo "install step"
 pre_build:
   commands:
     - echo "pre_build step"
 build:
   commands:
     - aws s3 sync --delete . "s3://<your-static-website-bucket>"
 post_build:
   commands:
     - echo "post_build step" 
```

Enter fullscreen mode Exit fullscreen mode

但是，我们可以在`post_build`部分增加一个额外的步骤。看看我们下面的新文件。

```
version: 0.2
phases:
 install:
   commands:
     - echo "install step"
 pre_build:
   commands:
     - echo "pre_build step"
 build:
   commands:
     - aws s3 sync --delete . "s3://<your-static-website-bucket>"
 post_build:
   commands:
     - aws cloudfront create-invalidation --distribution-id <your-distribution-id> --paths '/*' 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们添加了 AWS CLI 调用来使我们的 CloudFront 发行版在`post_build`步骤中无效。如果用 CloudFront 发行版的 ID 替换`<your-distribution-id>`,那么每次部署时缓存都会自动失效。

简单吧？我们结合了我们所学的一切，并开始优化手动任务。现在，我们可以快速开发我们的静态网站，持续部署它，并通过在 CloudFront 的有效缓存来优化对用户的交付。

### 几件事要注意…

这是对我们静态网站用例的一个极好的增强。我们已经学会了如何利用现有的服务来优化向用户交付资源。

然而，根据您的用例，有几件事情值得注意。

1.  我们所有资源的默认 TTL 设置为一个月。通过我们新的自动缓存失效，我们可以将这个时间延长到更长。也许一旦你的网站开发完成，一年是有意义的？
2.  不同的资源可以有不同的缓存周期。这里我们使用我们唯一的行为，默认行为。但是您可以为所有人添加一个行为。png 图像，并对该类型的资源采用不同的缓存策略。
3.  我们没有给存储在 S3 的资源添加任何缓存头。因此，浏览器不会试图在本地缓存任何资源。

最后一点完全可以通过再次调整我们的`buildspec.yml`文件来实现。

```
version: 0.2
phases:
 install:
   commands:
     - echo "install step"
 pre_build:
   commands:
     - echo "pre_build step"
 build:
   commands:
     - aws s3 cp . "s3://<your-static-website-bucket>" --recursive --cache-control 'public, max-age=2628000'
 post_build:
   commands:
     - aws cloudfront create-invalidation --distribution-id <your-distribution-id> --paths '/*' 
```

Enter fullscreen mode Exit fullscreen mode

注意我们的`build`步骤已经改变。这里我们调用 copy 命令将当前目录的内容复制到我们的 S3 网站桶中。我们给我们的调用添加了一个新的标志，`--cache-control`，并给它赋值`public, max-age=2628000`。

当我们的资源被复制到我们的 S3 存储桶时，CLI 将用我们指定的值为每个资源添加一个缓存控制头。这允许我们在浏览器和 CloudFront 发行版中利用本地缓存。

### 结论

在这篇文章中，我们探讨了如何利用我们现有的在 AWS 上托管、保护和交付静态网站的知识来进一步优化。

我们展示了如何调整我们的 CloudFront 发行版，以获得更长的默认缓存期。为了防止我们的用户出现问题，我们使用 AWS CodeBuild 和 CodePipeline 通过 CI/CD 管道将缓存失效添加到我们的构建过程中。然后，我们看到了如何调整资源复制到 S3 的方式，以便附加浏览器可以识别的缓存头。

Amazon Web Services 是一个非常强大的平台，为各种用例提供了许多不同的服务。最好通过将这些服务应用到您所面临的问题中来开始学习这些服务。一旦你学会了一项服务的基础，你就可以像我们在这里所做的那样，开始深入挖掘，从中挖掘更多。

### 您是否渴望了解更多关于亚马逊网络服务的信息？

如果你想开始你的 AWS 之旅，但却不知道从哪里开始，可以考虑查看我的课程。我们专注于在 AWS 上托管、保护和部署静态网站。让我们在使用时能够了解超过 6 种不同的 AWS 服务。在你掌握了基础知识之后，我们可以进入**的两个额外章节**来讨论更高级的主题，比如基础设施代码和持续部署。