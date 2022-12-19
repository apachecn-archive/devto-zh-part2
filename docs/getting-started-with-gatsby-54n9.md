# 盖茨比入门

> 原文：<https://dev.to/jaybeekeeper/getting-started-with-gatsby-54n9>

所以不久前，我写了关于使用 Jekyll 创建一个网站[的基础知识。我用 Jekyll 建立了我的第一个个人网站！我真的很喜欢这种简单。](https://dev.to/jaybeekeeper/getting-started-with-jekyll-3nf9)

但是时代变了。口味变了。我在重建我的网站，这次我用的是盖茨比！

和 Jekyll 一样，Gatsby 可以作为静态站点生成器。它非常适合作品集网站、博客、个人网站等。而静态站点很容易使用 Github pages、Netlify、Surge 等进行部署。但是让盖茨比特别让我兴奋的是它是一个为*反应*的站点生成器。

如果你知道 React，如果你已经使用 React 构建了一个动态的 web 应用程序，你就已经在做生意了——Gatsby 正在使用 React 库。

所以，就像我之前对哲基尔所做的那样，让我们快速浏览一下盖茨比。

假设您已经在机器上设置好了节点和节点包管理器，让我们进入命令行，安装 Gatsby 的 CLI 工具，并生成一个新站点。

```
$ npm install --global gatsby-cli
$ gatsby new my-new-site https://github.com/gatsbyjs/gatsby-starter-default 
```

Enter fullscreen mode Exit fullscreen mode

我们这里的第一行只是在我们的机器上安装了 Gatsby CLI 包。我们的第二行是这里有趣的部分:`gatsby new`是我们生成一个新站点的命令。“我的新网站”将是生成的目录名。在这个目录中，我们将拥有开始使用的所有工具。最后的网址是什么？那是我们的“开胃菜”。从本质上来说，启动程序是我们网站的一个主题或模板。。我们在这里使用默认值。

这是我们得到的文件结构:
[![](../Images/c1381d5bf54b424450f073c6df404a38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FmIHY-R---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/svftvd59qzibtk0sy81x.png)

它看起来比哲基尔的设定要复杂一点！

在这个阶段，我们可以使用盖茨比提供给我们的一整套命令。

这将建立一个本地开发环境，在每次更改时重新加载！
`gatsby build` -这将生成一个生产版本，即您的静态站点，用于您选择的部署服务(surge、Netlify 等)。)
`gatsby serve`——这将为你构建的站点设置一个测试环境。

让我们关注一下`gatsby develop`——运行这个程序，您将在 Localhost:8000 上启动一个服务器。我的看起来像这样！

[![](../Images/8bc6471f7a6fe0e966c6b18cb6e97bb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IIH2-nCr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zmksj3wzv8zo777gw6vs.png)

所有这些内容都是使用默认的 Gatsby starter 提供给我们的？那么，如何深入下去，并实际做一些编辑呢？您可以直接进入 src 文件夹，编辑组件，就像任何旧的 React 应用程序一样！为了说明，我将添加一个非常基本的组件。

如果你不熟悉 React 组件，简单的解释是它只是一个返回 HTML 的 Javascript 函数。比这多一点，但对我们的目的来说也够了。

所以我们默认的布局看起来如下:
[![](../Images/a91958d953d3c3d58ec130f910980dac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xAvd_BhO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/frilis33vafczay3zstv.png)

我要创建一个新的。js 文件来构建这个示例组件，如下所示:

[![](../Images/bd45c6bf0ddd4d71825e1b0fb4492a10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--idvm2zM7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qeipmzvz6apafxmmk0v8.png) 
(注意，我导入了 React 来构建这个组件！一切都只是反应。

然后，让我们将新的示例组件导入到我的索引中。
[![](../Images/4e776f5711d29c15dc497431b00308fc.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--oP38Tx5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jt2j94pylj1lswtxd2be.png)

那很简单！我们的网站现在是什么样子？
[![](../Images/73471c4693456c0ea0cd8607a1e5b0c5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--G1spHQjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/czcvhf3kfg7in7t45zot.png)

好的...所以它不是最漂亮的——但是我们看到了构建 HTML 内容并把它放在我们的页面上是多么容易！就像 React 一样，我们可以将道具传递给子组件，导入库，构建基于类的组件或功能组件，等等。

我目前正在建立一个新的作品集网站，这是正在进行中的工作的一个高峰，是用 Gatsby 建立的:

[![](../Images/3f5f58f2a32f665f9a1f15b8afa1f44f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v_wb6Aid--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q189lnaggqkzkkeop606.gif)

我还没说完！这是一项正在进行的工作。但是这很容易做到，因为 React 库的模块化组件结构，以及它为我提供的灵活性。这是一个简单的范例，但却非常强大，适合如此多的定制。

当我准备好的时候，我将运行`gatsby build`来生成一个公共文件夹——我可以将它部署到我选择的静态服务中！真的就那么简单。

深入研究下面的 Gatsby 和 React 文档，并开始构建！

[盖茨比](https://www.gatsbyjs.org/)
[盖茨比教程](https://www.gatsbyjs.org/tutorial/)
[反应元件](https://reactjs.org/)
[反应元件](https://reactjs.org/docs/components-and-props.html)