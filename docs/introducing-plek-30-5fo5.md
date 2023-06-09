# Plek 3.0 简介

> 原文：<https://dev.to/siilwyn/introducing-plek-30-5fo5>

Plek 是像 [ZEIT Now](https://zeit.co/now) 和 GitHub 这样的无服务器服务之间的粘合剂，改善你的持续部署工作流程。[无服务器](https://martinfowler.com/articles/serverless.html)和持续部署就像面包和黄油一样密不可分，Plek 通过定义的流程和对 GitHub 的反馈强调了这一点。每个部署状态都报告给 GitHub，并根据分支分配一个域。在主分支上，部署直接转到您的域，对于拉请求，子域用于预览更改。从而形成一个快速的反馈循环，项目中的每个人都可以很容易地查看变更。

[![GitHub PR checks](img/08e21aa875cb061e16b7f38a41c86ed2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7uIedbEN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/smst1psrnmx8bdcz2tdz.png)

## 流量

这听起来像是 GitHub 最近宣布的 [ZEIT Now 吗？是的，这些解决方案肯定是重叠的。当发布 Plek 时，这并不适用，因为 GitHub 大约三周后才发布。但是它们服务于不同的用例，因为 Plek 是作为 CLI 工具从您的 CI 中运行的。这是责任上的区别，你可以控制 Plek 运行的方式和时间。这种灵活性在以下情况下非常方便:](https://zeit.co/docs/integrations/now-for-github)

*   部署 monorepos，一个包含多个包或项目的存储库；
*   部署前运行测试；
*   在 Node.js 项目中使用私有 npm 包。

而 Now for GitHub 是有限的，但更容易运行，因为它从 Now 的环境中运行，这意味着不需要 CI 设置，但也限制了可能性。因此，如果 Now 到 GitHub 管道是您所需要的，那么 Now 的集成工作得很好。但是如果你想要更多，使用 Plek，我提到过它也是开源的吗？在 **[voorhoede/plek](https://github.com/voorhoede/plek/)** 查看。

## 飞

Plek 与 CI 和服务无关，目标是支持多种无服务器服务和 git 平台，如 GitLab。为了证明 Plek 是超级灵活的，我们刚刚发布了 Plek 3.0，增加了 [Fly](https://fly.io/) 作为服务，更多即将推出！Fly 是一个开源的 JavaScript 运行时，它让您能够创建自己的可编程 CDN。有了这种能力，您可以部署从负载平衡器到静态网站的任何东西，结合 Plek，它很容易部署和测试。

[https://player.vimeo.com/video/299021064](https://player.vimeo.com/video/299021064)

## 结论

无服务器是部署应用程序、部署代码并让服务来处理的一种经济高效且简单的方式。有了这种能力，持续部署成为一种自然的生活方式。ZEIT Now 和 Fly integration from your CI 只需一个命令就可以使用`plek now`和`plek fly`。

 [![Let's get started](img/726f10c311d14e0bd926d03d28107d71.png)
T4】](https://plek.now.sh/guide/getting-started.html)