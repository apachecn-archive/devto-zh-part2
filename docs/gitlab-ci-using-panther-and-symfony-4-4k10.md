# 使用 Panther 和 Symfony 4 的 Gitlab-CI

> 原文：<https://dev.to/chokri/gitlab-ci-using-panther-and-symfony-4-4k10>

我试图用 gitlab-ci 测试我的 Symfony 4.1 应用程序。基本上，当我们测试 PHP 应用程序时，我们需要 php-cli docker 映像来执行 composer install，phpunit。但是在这种情况下，我需要安装一个节点包，在前端测试一个 React 应用程序，并使用我的 React 应用程序测试一个 Rest API。

我在前面使用 React Routes 创建了一个简单的 Symfony 4.1 应用程序，并执行 fetch 从 ApiController 获取 JSON。

我用 Panther 做了一个测试用例，在本地进行了测试，效果很好。

它花了 9 秒钟，因为在控制器中我写了在返回响应之前睡眠 8 秒钟。

然后，我想使用 Gitlab-CI 测试我的应用程序，当我推送更新时，我想确保一切正常，我可以安全地将我的应用程序部署到生产环境中。

我写了一个 gitlab-ci 来测试这个应用程序，它失败了…它失败了…它失败了…我检查了日志，我尝试了…最后我决定写我自己的 Docker 映像并用它来测试我的应用程序。

我基于 Ubuntu 18.04 制作了这个 [Docker 镜像](https://hub.docker.com/r/chokri/phpnode/)。它包含 PHP 7.2 和 Node JS。为了能够运行 Panther 单元测试，我们需要安装 3 个包，(ZIP，Libnss3 和 chromium-browser)。所有这些东西都可以在一个地方买到。问题是图像的大小大概是 1.18GB！！

有了这个 docker 映像，我现在可以在 gitlab 上运行 CI 测试了。

[![](img/ffd56717939e84e18fd9fadc328b1152.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RLg_BgVU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/khalifa.tn/wp-content/uploads/2018/10/Capture-d%25E2%2580%2599e%25CC%2581cran-2018-10-09-a%25CC%2580-11.04.36-AM.png%3Fw%3D646%26ssl%3D1)

[![](img/67128852126c296d58b584b245d1afb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ApEhpbgG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/khalifa.tn/wp-content/uploads/2018/10/Capture-d%25E2%2580%2599e%25CC%2581cran-2018-10-09-a%25CC%2580-11.05.57-AM.png%3Fw%3D646%26ssl%3D1)

使用 Panther 和 Symfony 4 的 Gitlab-CI 帖子首先出现在 [C.Khalifa](https://khalifa.tn) 上。