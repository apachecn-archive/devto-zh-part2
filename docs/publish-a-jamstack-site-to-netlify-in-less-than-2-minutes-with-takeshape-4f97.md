# 用 TakeShape 在 2 分钟内将 JAMstack 站点发布到 Netlify

> 原文：<https://dev.to/takeshape/publish-a-jamstack-site-to-netlify-in-less-than-2-minutes-with-takeshape-4f97>

## 在这个 4 部分系列的第 3 部分中，您将学习如何向 Netlify 发布一个静态站点

这是关于用 Takeshape 建立投资组合的 4 部分系列的第 3 部分。查看[第 1 部分](https://www.takeshape.io/articles/how-to-build-a-design-portfolio-with-takeshape/)，了解如何建模和创建内容。[第 2 部分](https://www.takeshape.io/articles/how-to-build-a-jamstack-website-with-takeshape/)向您展示如何创建一个从 TakeShape 加载数据的静态站点。在第 4 部分(即将推出)，您将进一步扩展您的技能，并学习如何使用 TakeShape 和 Gatsby.js 构建一个基于 React 的静态站点

如果您一直关注这个系列，那么您已经有了一个灵活的内容模型和一个静态站点。现在，您将学习如何使用带有 TakeShape 的 [Netlify](https://www.netlify.com/) 在短短两分钟内部署您的实时站点。这里有一个[演示](https://shape-portfolio.takeshapesampleproject.com/)你将部署什么。通过一键式集成，TakeShape 可以轻松地让您的新网站上线，在 CDN 上运行并使用 HTTPS。

> 如果你刚刚加入，不要担心。你可以通过[注册一个免费的 TakeShape 账户](https://app.takeshape.io/signup)，创建一个模板“Shape Portfolio”项目，并从 GitHub 克隆[“take Shape-samples”库](https://github.com/takeshape/takeshape-samples)来快速赶上。创建模板项目后，打开终端，用命令`git clone https://github.com/takeshape/takeshape-samples.git takeshape-samples && cd takeshape-samples/shape-portfolio`克隆并访问样例 repo。现在我们都在同一页上了！

## 形状和生命形影不离，就像花生酱和果冻

在开始本演练的其余部分之前，这里有一个快速入门，介绍为什么 TakeShape 和 Netlify 以您的内容为中心制作如此美味的 JAMstack 三明治。

Netlify 是一个强大且易于使用的平台，用于部署、维护和升级您的静态站点。开箱即用，Netlify 提供强大的托管、持续集成工具和自动 HTTPS 的默认安全性。随着你的站点的成长，Netlify 帮助你消除静态站点早期面临的许多障碍。它们将为您提供表单提交的端点、无服务器功能，甚至让您管理用户身份。简而言之，它们给了你静态的所有优点，没有缺点。

更好的是，TakeShape 可以直接与 Netlify 集成。通过一键式集成，您可以为 TakeShape 项目快速设置静态站点发布目标，并在几分钟内让您的新项目上线。有了 TakeShape 处理您的内容和 Netlify 托管您的站点，您可以自由地关注重要的事情:您的内容和代码。

## 将您的 TakeShape 项目连接到 Netlify

现在，您将通过将 TakeShape 项目与 Netlify 集成来测试这些大胆的主张。你很快就会发现它名副其实！

首先登录或注册一个 [Netlify](https://app.netlify.com/signup) 账户。你可以免费这样做，甚至可以使用你现有的 GitHub、GitLab 或 BitBucket 账户。

TakeShape 已经提供了一些全面的指令来在你的项目上配置 Netlify，所以我们将保持简短。

[![TakeShape integration with Netlify](../Images/90ff20c9e1e49c81cfdf32844cea6990.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8zgyi50h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7adna9ry865w69ok3ue7.gif)

首先，转到项目设置>集成，将您的 Netlify 帐户与您的 TakeShape 项目关联起来。然后，转到静态站点并创建一个新的静态站点。给它起一个合适的名字，并选择 Netlify 作为提供者。然后，对于目的地，选择+创建新的静态站点。“名称”和“密码”字段都是可选的。如果您不选择名称，Netlify 会自动为您生成一个。您可以稍后在网络管理员中编辑该名称和密码。最后，单击保存。

噗！那不是很容易吗？

## 将静态站点部署到网络

您的站点模板将经历以下旅程:

本地开发环境> TakeShape 环境>网络虚拟主机

现在，您已经让 TakeShape 和 Netlify 相互对话，您已经准备好使用 CLI 部署您的站点了。部署您的网站会将您的模板和配置上载到 TakeShape 的云环境，以便您可以在将项目上载到 Netlify 之前构建和发布项目。

在您的终端中，`cd`到您本地机器上的 TakeShape 项目所在的文件夹。接下来，您将使用`npm run init`重新初始化您的 TakeShape 项目，输入您的 TakeShape 用户名和密码，并选择您新配置的“Netlify”静态站点作为部署目的地。您应该会看到新生成的文件`.graphqlrc`、`.tsgrc`和`graphql.config.json`，以及一条成功消息和一条不要将这些文件添加到您的版本控制中的警告。

现在是关键时刻了。运行`npm run deploy`。如果一切顺利，您将看到一条成功消息。如果事情没有按预期进行，仔细阅读终端中的错误消息输出，尝试调试问题。

> **奖励点:设置与 Netlify 的持续集成**
> 由于 Netlify 有内置的持续集成，如果您的项目位于 GitHub、GitLab 或 BitBucket 中，那么当您向分支推送新的提交时，可以将您的站点设置为自动部署。TakeShape 已经[提供了一个简单的指南](https://www.takeshape.io/docs/continuous-deployment-of-templates/)来设置和运行，当你建立你的网站时，它真的可以提高你的生产力。

## 从网络发布

当您从 CLI 部署站点时，TakeShape 会自动发布一次。如果您添加或更改项目内容，您可以从 TakeShape web 客户端发布更改，方法是在项目下拉菜单中选择“发布[您的静态站点名称]”。

从“项目”菜单中，当您选择“发布 Netlify”时，TakeShape 将开始构建项目的新版本，并将其上传到 Netlify。您将在一两秒钟内看到一条成功消息。

这就是全部了！正如您所见,'形状'和'网络'相互辉映，美不胜收。只需几分钟，您就可以拥有一个安全、快速、完全可定制的静态站点。

## 下一步

真的，我们可以就此打住，因为你已经有了一个静态网站，它与 TakeShape 和 Netlify 一起工作得很好。但在我们演练的最后部分，我们将踢它一个档次，如 Emeril Lagasse 说。我们将向您展示如何将您的静态站点提升到下一个级别。通过使用 Gatsby.js 作为静态站点生成器，使用 React 组件构建您的投资组合，您将获得新的能力和灵活性。太刺激了！

## 对静态网站的 CMS 感兴趣？

TakeShape 是一个用于构建 JAMstack 网站的无头 GraphQL CMS 和静态站点生成器。在 TakeShape，我们正在为最具创造力的设计师和开发人员打造最好的内容管理工具。我们的项目模板让您轻松入门。此外，价格完全可以承受。[注册一个免费账户](https://app.takeshape.io/signup)，花更多的时间发挥创造力！