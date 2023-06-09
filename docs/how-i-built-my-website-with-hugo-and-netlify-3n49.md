# 我是如何与 Hugo 和 Netlify 一起建立我的网站的

> 原文：<https://dev.to/mokkapps/how-i-built-my-website-with-hugo-and-netlify-3n49>

去年年底，我开始着手我的私人投资组合网站 T1，并开始研究如何轻松地建立和部署这样的静态网站。

# 工具

## 雨果

我发现了 Hugo，这是一个非常流行的开源静态站点生成器。这是惊人的快，非常灵活，它使建立网站的乐趣与此发电机。

只需按照[官方“快速启动”](https://gohugo.io/getting-started/quick-start/)操作，不到五分钟你就在你的机器上本地运行一个漂亮的静态网站了。

有许多[主题](http://themes.gohugo.io/)可供选择，它们通常是高度可定制的。

基本工作流程是这样的:

*   使用`hugo serve`在本地服务您的网站
*   如果你完成了，你生成静态网站内容运行`hugo`
*   发布生成的网站内容(见下一章)

## Netlify

Netlify 提供了一个自动化代码的平台，以创建高性能的网站和网络应用。基本上你只需要推送你的代码，剩下的就交给 Netlify 了。

如果你的代码已经在 GitHub、GitLab 或 Bitbucket 上，设置 Netlify 是相当容易的。您只需要选择您的 Git 提供者，定义应该执行哪些构建命令，以及最终内容位于哪个文件夹中。

更多详情，请查看[官方“入门”指南](https://www.netlify.com/docs/#getting-started)。

Netlify 提供了一个我目前正在使用的免费订阅模式。此外，还有许多额外的功能，你必须支付。查看官方定价页面了解更多详情。

# 我的设置

我开始使用一个简单的静态网站，它是我在一个网络服务器上手动托管的，没有使用 Netlify 之类的服务。我用的自定义域是`www.mokkapps.de`。

几个月前，我决定开始我自己的关于软件开发主题的技术博客，我想继续使用 Hugo。因此，我不得不选择另一个 Hugo 主题，因为当前主题无法进行博客所需的内容管理。

经过短暂的研究，我发现 [KISS](https://github.com/ribice/kiss) 拥有我一直在寻找的风格和功能。博客文章是用 [Markdown](https://en.wikipedia.org/wiki/Markdown) 写的，我非常喜欢用它来写文章和其他基于文本的东西。

我希望可以通过`www.mokkapps.de/blog`访问博客，所以我必须使用 Hugo 生成博客页面，并将其放在 web 服务器的`blog`文件夹中。这是一个我想自动化的手动过程。

幸运的是，像 Netlify 这样的平台可以帮助自动化这些任务。我已经在 Netlify 中集成了两个网站，但仍然需要从一个 Hugo 网站连接到另一个。

Netlify 为这种情况提供了[重定向](https://www.netlify.com/docs/redirects/)。所以我在主页上添加了一个静态的`_redirects`文件，现在它可以正确地链接到 Netlify 上的第二个页面:

 `/blog/* https://mokkapps-blog.netlify.com/:splat 200` 

现在我所要做的就是写我的博客文章或在我的网站上做任何其他更改，并将它们推送到我的 Git 提供商。然后，Netlify 会自动构建和部署页面。

# 结论

使用 Hugo 和 Netlify 这样的服务来构建和部署网站非常有趣。我强烈推荐您看一看它们，也许您当前或未来的项目会需要它们。

# 链接

*   [源代码网站](https://github.com/Mokkapps/mokkapps-website)
*   [源代码博客网站](https://github.com/Mokkapps/mokkapps-blog)
*   [雨果](https://gohugo.io/)
*   [Netlify](https://www.netlify.com/)