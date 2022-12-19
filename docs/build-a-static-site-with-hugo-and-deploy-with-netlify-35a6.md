# 用 Hugo 构建静态站点，用 Netlify 部署

> 原文：<https://dev.to/lauragift21/build-a-static-site-with-hugo-and-deploy-with-netlify-35a6>

[![Hugo](../Images/9f46ec0aafd2998e1e4c79425548c3b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SCIPsO-F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://giuliovdev.gallerycdn.vsassets.io/extensions/giuliovdev/hugo-extension/1.0.0/1503297014062/images/hugo-logo.png)

我将重点介绍如何用 Hugo 构建一个快速站点，并在瞬间部署到 netlify。但在开始建造之前，我们必须了解雨果和奈利菲是什么。

[**Hugo**](https://gohugo.io) 是最流行的开源静态站点生成器之一。凭借其惊人的速度和灵活性，Hugo 让网站建设再次充满乐趣。

Netlify 是一个统一的平台，它自动化你的代码来创建高性能、易维护的网站和网络应用。

#### 第一步:安装 Hugo

为了能够使用 Hugo，我们需要为 Mac 或 Ubuntu 用户运行这个命令来安装它。

```
brew install Hugo 
```

Enter fullscreen mode Exit fullscreen mode

```
sudo apt-get install Hugo 
```

Enter fullscreen mode Exit fullscreen mode

#### 第二步:创建新的 Hugo 站点

```
hugo new site -siteName 
```

Enter fullscreen mode Exit fullscreen mode

#### 第三步:挑选一个主题

我最喜欢的 Hugo 功能之一是你可以从这里提供的各种主题列表中进行选择 [hugo themes](https://themes.gohugo.io/) ，继续选择一个主题，我将使用 hugo-hikari-theme。请确保您通读了使用该主题的说明。

将 theme.toml 复制到 config.toml 文件中。

#### 第四步:运行主题

```
hugo server 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到一个构建过程和一个类似这样的消息:

```
Server is available at http://localhost:1818/ 
```

Enter fullscreen mode Exit fullscreen mode

你可以根据个人喜好随意修改主题。

#### 第五步:部署到网络生活

这是最简单的一步，我们只需要创建一个 netlify 账户，将你的 GitHub 档案连接到 netlify。

[![alt text](../Images/52522e05fe12e85579fa2ac198c2c182.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6bZu6xq0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.netlify.com/6ce8bf46dcc8bfc6d6ef982c7870eb86e32d2b8c/89152/img/blog/step-2-hugo.png)

之后，从 Netlify 仪表板创建一个新站点，并在 GitHub 上连接您的远程存储库。

使用此设置创建部署命令并进行部署。

[![Screenshot](../Images/3a87ed389b91e415b40f261d022b2bb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vGt3FsGR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/lauragift/image/upload/v1519148146/Screenshot_from_2018-02-20_18-30-47_zoifgr.png)

Viola，通过这些快速步骤，你已经成功地用 Hugo 和 Netlify 构建并部署了你的站点。这篇文章最初发布在我的[博客](https://www.giftegwuenu.com/2018/03/20/build-a-static-site-with-hugo-and-deploy-with-netlify/)