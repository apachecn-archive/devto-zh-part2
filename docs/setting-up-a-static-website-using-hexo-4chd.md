# 使用 Hexo 建立静态网站

> 原文：<https://dev.to/arpitgogia/setting-up-a-static-website-using-hexo-4chd>

*这篇文章最初发表在我的[博客](https://arpitgogia.com/archives/)T3 上*

个人网站是展示你的内容和项目的好地方，或者只是给人留下一个好的第一印象。这个帖子是一个减轻负担的尝试，尤其是对新手来说。

### 你需要什么，为什么

1.  [己糖](https://hexo.io)
2.  [燃烧基地](http://firebase.google.com)

Hexo 是一个使用 JavaScript 构建的静态站点生成器。我在这里使用它没有特别的原因，我只是通过 Hexo :p 找到了一个很好的模板。还有其他的你可以试试，比如 Jekyll(已经看到这个被经常使用)，Hugo 等等。这里有一份详尽的[清单](https://www.staticgen.com)。

我在这里使用静态站点生成器的原因是为了简化创建 HTML 页面的过程。这样，您就不必创建适当的详细格式和响应所需的 CSS 和 JS 代码。静态网站生成器允许你使用最重要的信息，你的内容来创建网站。

### 选择主题

Hexo 有个大主题[库](https://hexo.io/themes/index.html)。任何要求都有模板。我网站的主题是[仙人掌](https://github.com/probberechts/hexo-theme-cactus)。如你所见，它非常简约，这正是我想要的。

### 定制

有趣的部分来了。Hexo 站点的基本部分是`_config.yaml`文件。 [YAML (YAML 不是标记语言)](http://yaml.org)是一种配置文件格式，由于语义明显，易于阅读，旨在“最小化”。YAML 是一种人类可读的数据序列化语言。它通常用于配置文件，但也可以用于存储或传输数据的许多应用程序中。这里是 Hexo 提供的设置列表。还可以轻松支持使用 Disqus 整合评论，使用 Google Analytics 整合分析。

在这一点上，如果你对你的网站的外观满意，你可以继续到部署部分，将它部署到你选择的主机服务上。或者，您可以深入研究，开始调整 CSS，直到您对外观满意为止。

你可以通过在放置`_config.yaml`的目录下的终端中键入`hexo generate && hexo server`来测试你的网站。

### 部署

我选择 Firebase 进行部署，因为它使用起来相对简单，而且我有点偏爱 Google 产品:p. Hexo 的[文档](https://hexo.io/docs/deployment.html)提到了相当多的托管站点的方法。

继续，假设你已经安装了 Firebase CLI 并且[登录了](https://firebase.google.com/docs/cli/)，继续使用`firebase init`初始化你的项目，与你的`_config.yaml`在同一个目录中。现在只需再下一个命令`hexo generate && firebase deploy`就行了，你的网站已经部署好了！如果您想模拟 firebase 环境进行本地测试，您也可以做`hexo generate && firebase serve`。这与`hexo server`有点不同，可以用于你的站点的本地测试。

好了，你现在有了一个功能齐全的网站。

如果您对以上说明有任何疑问或建议，请在 [Twitter](https://twitter.com/arpit_gogia) 上联系我。