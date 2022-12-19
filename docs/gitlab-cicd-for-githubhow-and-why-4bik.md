# GitHub 的 git lab CI/CD——如何以及为什么？

> 原文：<https://dev.to/anoff/gitlab-cicd-for-githubhow-and-why-4bik>

### GitHub 的 git lab CI/CD—如何以及为什么？

当创建一个 git 项目并希望与他人分享时，传统上你需要在 GitHub 和 GitLab 之间做出选择，GitHub 拥有庞大的社区和大量的集成，git lab 拥有从问题到最佳 CI/CD 解决方案的出色整体开发经验，BitBucket 是你从幼儿园起就有的朋友之一。我个人的决定是在[举办我所有的个人项目🦑GitHub](https://github.com/anoff) 。对于需要 CI/CD 的项目，我与[一起修修补补👷GitHub 上的‍♂️ Travis CI](https://travis-ci.org/) 和 [🅾️ Circle CI](https://circleci.com/) 。

<figure>[![](../Images/75f18316af53a64c3743df39d24d1a5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LCg2O1fc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/912/1%2AlaA78JtrefVUbLNEXjw_cg.png) 

<figcaption>它不再是 GitHub 或者 GitLab</figcaption>

</figure>

但是最近 [GitLab 宣布](https://about.gitlab.com/features/github/)他们惊人的 CI/CD 解决方案现在可以与 Github 上托管的 git 项目相结合。在过去的 1.5 年里，我一直在使用 fullstack GitLab 解决方案，我真的很期待在我的个人项目中测试它。现在，您终于可以拥有两个世界的精华了——社区中的 GitHubs reach 和 GitLabs CI 工具💃

### 如何为 GitHub 项目启用 GitLab CI/CD 管道

乍一看，GitLab 像任何其他 CI/CD 服务一样集成到您的项目中。使用服务自动为您添加或您手动定义的 webhooks。GitLab 在描述[如何设置这些](https://docs.gitlab.com/ee/ci/ci_cd_for_external_repos/github_integration.html)方面做得很好，所以我不会详细重复这些步骤。

<figure>[![](../Images/c2a48deffa68c775f00b3845c9b8fcca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TaFiCtsk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/729/1%2A0yMTq5LZdnbUwp7ykpHppw.png) 

<figcaption>CI/CD 通过 webhook 集成到 GitHub</figcaption>

</figure>

要设置它，你首先需要在 GitHub 上安装你的项目。我设置了一个小[节点。JS web app](https://github.com/anoff/node-ci-dummy) 来玩集成。请随意使用它或创建您自己的。接下来你前往 [GitLab](https://gitlab.com/) 登录——我建议使用 GitHub OAUTH 将你的代码凭证保存在一个地方。要设置您的 GitLab CI/CD 项目，只需创建一个新项目，并在最右边的 CI/CD 中选择外部回购选项，然后单击 GitHub。

[![](../Images/875ad098d37f053e55e2316aa1bc1d30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qs5dGnLU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/670/1%2AZNUtH4F0iN7XtRScmcRQcw.png)

[![](../Images/a54e8aa361bd880a4a1f76f8fbb876d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O0wpaGB1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiIzoz5QkpUTdz8nMOYFmtg.png)

你会看到一个你的 GitHub repos 列表，点击你想用于这个集成的连接。在我的例子中，它是 repo anoff/node-ci-dummy

[![](../Images/b1286bd76c42e5816de8d0c1daea6f4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tsf9QIls--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1vQDUTpD07NZ-A4_44LrqA.png)

[![](../Images/8bbc974579874da12d4400995241f8da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7XiNw1dU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8cQIG5Jw6i_FlEfcVE-MJg.png)

这样做之后，你会看到 GitLab 实际上克隆了你的整个项目。有两个设置，以确保这个神奇的🧙‍♀️工程。一个是之前提到的 GitHub 端的 webhook，当你把 git 推送到你的 GitHub 库时，它就会触发。另一个是您新创建的 GitLab 项目的存储库设置，它被设置为从远程存储库——也就是您选择的 GitHub 项目——获取。

[![](../Images/9680707c07d013caf9292abafdd6a3e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3TzI-DDL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKJfhmNFMagGt6AxTTOj0eg.png)

<figure>[![](../Images/e0610158ecea851bf17c8c80cfb4cba3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2IEFEdlZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2lI94T72HdmERMgxYJKr_Q.png)

<figcaption>GitHub web hook(左)和 GitLab 回购镜像选项(右)</figcaption>

</figure>

### 如何配置配置项管道

最后要做的事情是在您的存储库中建立一个实际的 CI 管道。GitLab 将自动识别管道定义并运行定义的作业。如果您不熟悉 GitLab CI，您可能希望从最简单的管道定义开始。只需在根目录下创建一个. gitlab-ci.yml。测试节点的示例内容。JS app:

```
image: node:9
lint:
  script:
  - npm install
  - npm run lint

unit test:
  script:
  - npm install
  - npm test 
```

Enter fullscreen mode Exit fullscreen mode

在我的虚拟存储库中，这将并行运行[标准](https://standardjs.com/)林挺和 [ava](https://github.com/avajs/ava) 单元测试。下面你可以看到引入这个文件的提交和相应的被触发的的[管道。](https://gitlab.com/anoff/node-ci-dummy/pipelines/19765773)

<figure>[![](../Images/a2ee9c1e96e28a6ed982e42387266408.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a8YtmAza--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/966/1%2AeeV-370AqhtP_u7pn4vUyw.png) 

<figcaption>GitHub 提交视图与一个挂起的 GitLab 管道</figcaption>

</figure>

<figure>[![](../Images/a6e1b6d248d9c3de55c66db99c545932.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d-9IX1qp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkyIC64kaAg1JGARmQnKieQ.png) 

<figcaption>GitLab 在建管道视图</figcaption>

</figure>

<figure>[![](../Images/ad2b4603f4a318dbd37662c74291f648.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Etra4Ngv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/272/1%2AMx5QNLDV3WDJbLVQzg8DjA.png)

<figcaption>CI 管道完成后成功标记在 GitHub 上</figcaption>

</figure>

查看[文档](https://docs.gitlab.com/ee/ci/yaml/)了解更多关于编写定制管道的信息。例如，引入阶段可以帮助您处理更复杂的管道工作流。如果你正在运行一个公共 GitHub 项目，并且你想让客人详细地看到你的 pipeline 结果，你应该确保你的 GitLab 项目被设置为**公共**。

[![](../Images/4911b6a1454b4f462e6a44fd56f32d1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KT_gjXn4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/882/1%2ACwOSDGb_fuuDyIEeBYzHyA.png)

### 最后的想法

这篇文章讲述了如何在 GitHub 和 GitLab 之间建立一个基本的集成工作流程:没有那么多工作🏋实际上是你的️‍♀️。

接下来要做的事情:

*   将[多个阶段](https://docs.gitlab.com/ee/ci/yaml/#stages)引入您的渠道
*   针对各种[运行时间](https://docs.gitlab.com/ee/ci/yaml/#image-and-services)进行测试
*   利用 GitLab 管道中的服务标签生成一个数据库来运行较小的集成测试

在我看来，与 Travis、Circle、Drone 等其他解决方案相比，GitLab CI/CD 的唯一缺点是，GitLab 为您创建了一个功能齐全的项目这一事实可能会给人们带来一些困惑。在这种情况下，你一定要做的一件事就是遵循上面的权限设置，禁用 GitLab 仓库上的*问题*、*维基*、*拉请求*。这样，您的权限将阻止人们通过 GitLab 与您交互。此外，您的项目会自动显示为镜像，从该镜像链接回您的 GitHub 主项目。如果这还不够，你可以在你的自述和投稿指南中添加一个链接。

<figure>[![](../Images/d0c9ac0c15fb6b2f957fb103cc42207e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5eevcCJD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/989/1%2A_pSl5B56gb6Ft7-4IHAmjg.png) 

<figcaption>GitLab 项目显示到原项目的链接</figcaption>

</figure>

哦，顺便说一句，如果你还在寻找标题所承诺的**为什么**: git labs CI/CD 解决方案是我认为最全面的解决方案之一。

欢迎在 [twitter](https://twitter.com/an0xff) 上讨论或发表评论👍