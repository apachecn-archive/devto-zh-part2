# 对于将仅前端 React 应用程序部署到生产环境，您有哪些选择？

> 原文：<https://dev.to/ucorina/what-options-do-you-have-for-deploying-a-frontend-only-react-app-to-production-20bo>

如果你曾经想知道**如何部署**你的 React 应用程序，一旦它准备好了，你可能知道弄清楚这一点会变得**非常混乱**。

大多数文章都详细介绍了如何配置 nginx，如何配置反向代理。但是如果你只有一个**前端专用的 React 应用**并且正在寻找最简单的部署方式，你真的需要这些吗？

如果你想一想，一旦你为生产建立了一个 React 应用程序，你基本上只剩下一堆`html`、`js`和可能的图像文件。这意味着你可以将它部署到**任何支持静态文件托管**的主机上。

让我们回顾一下一些选项！

## 火基

[网站](https://firebase.google.com/docs/hosting/) | [如何](https://dev.to/sathish/deploying-my-first-react-web-app-to-firebase---36bd)

Firebase 是谷歌的一项服务，也提供静态网站托管。它附带一个免费层，您可以从它开始，部署是通过 CLI 完成的，并且它们支持 SSL、CDN 和开箱即用的自定义域。

## Netlify

[网站](https://www.netlify.com/) | [如何](https://www.slightedgecoder.com/2017/12/09/deploying-existing-create-react-app-github-netlify/)

最近获得了很多关注。这是一个非常强大的点击式界面，允许您基于特定分支配置自动部署，为功能分支进行“测试”部署，甚至从用户界面对您的网站进行 A/B 测试。

## Surge.sh

[网站](https://surge.sh/) | [如何](https://dev.to/dceddia/deploy-create-react-app-with-surge-coc-temp-slug-7041928)

Surge 也是一个静态网站托管服务，专门面向前端开发者。支持自定义域，即使是免费计划。

## Zeit Now

[网站](https://zeit.co/now) | [如何](https://zeit.co/docs/examples/create-react-app)

部署您的网站就像在文件夹中运行`now`一样简单。通过他们的命令行界面支持许多选项。也是部署`node`应用或服务器端渲染 React 应用的绝佳选择。

## Github 页

[网站](https://pages.github.com/) | [如何](https://itnext.io/so-you-want-to-host-your-single-age-react-app-on-github-pages-a826ab01e48)

这是一个经典，可能是第一选择，如果你只是想为你的图书馆主页托管。也可以用于其他东西，因为它与 git 等有很好的集成。

因为它不是明确针对 spa 的，所以在设置路由时可能会遇到问题。

## AWS S3 木桶

[网站](https://aws.amazon.com/s3/) | [如何](https://www.fullstackreact.com/articles/deploying-a-react-app-to-s3/)

这是迄今为止描述的最复杂的选项，但是具有更大的灵活性，如果您已经将 AWS 用于其他事情，可能会很方便。将它与 AWS CloudFront 和 AWS Route 53 一起使用，获得完整的亚马逊解决方案([在此描述](https://aws.amazon.com/blogs/mobile/deploy-a-react-app-to-s3-and-cloudfront-with-aws-mobile-hub/))。

## Heroku、数字海洋、Azure 或亚马逊 EC2

您可以随时将您的应用程序部署到自定义服务器实例。这需要调整服务器，但给你更多的控制。您需要在机器上安装和配置一个 web 服务器 nginx、apache、IIS 或您更喜欢的任何服务器——并让它为您的静态文件服务。

虽然如果您已经有一台用于其他项目的机器，那么使用自定义设置很有吸引力，但是我强烈建议您尝试上面的选项之一，以将您的工作流程提升到下一个级别:)