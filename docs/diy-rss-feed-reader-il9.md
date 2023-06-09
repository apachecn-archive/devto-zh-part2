# DIY RSS 提要阅读器

> 原文：<https://dev.to/teej/diy-rss-feed-reader-il9>

有一大堆服务和应用程序可以整理你自己的个性化 RSS 源列表，并发现新的 RSS 源。它们都很好用，我很乐意推荐其中的一些。

然而，我是一个简单的人，有着简单的需求。事实上非常简单，所以我想我应该自己做一个。我知道这是怎么回事，我越是重新输入和阅读这句话，我就越讨厌自己，但是我不需要太多的功能；我想有一个提要列表，一个提要的文章列表，和一个单篇文章的视图。我不需要“保存以备后用”功能；我可以用 Instapaper 做这个。我不需要分享它们的方式；我可以用缓冲剂之类的东西。我想读点东西，仅此而已。

## 喂我

我喜欢 JavaScript，拥有一个可以迭代的 JSON 对象是最理想的。但是，我如何获得 JSON 呢？在 NPM 上敲了几下键盘后，我找到了 [rss 解析器](https://www.npmjs.com/package/rss-parser)。这正是我在寻找的；您将一个 URL 传递给一个提要，然后获取 JSON。

不过，这太直接了。我该如何让它变得复杂呢？

我创建了一个小项目，使用 [Express](https://expressjs.com/) 来响应 GET 请求，该请求返回我的提要数据。

```
const express = require('express');
const Parser = require('rss-parser');
const PORT = process.env.PORT || 5000;

const FEED_LIST = [
  'https://css-tricks.com/feed/',
  'https://codepen.io/posts/feed',
  'https://blog.safia.rocks/rss',
  'https://hnrss.org/frontpage',
  'https://tj.ie/feed.rss',
  'http://github-trends.ryotarai.info/rss/github_trends_javascript_daily.rss'
];

let app = express();

app
  .get('/', (req, res) => {
    let parser = new Parser();

    const feedRequests = FEED_LIST.map(feed => {
      return parser.parseURL(feed);
    })

    Promise.all(feedRequests).then(response => {
      res.setHeader('Access-Control-Allow-Origin', '*');
      // res.setHeader('Access-Control-Allow-Origin', 'some-domain-to-allow.com');
      res.header('Access-Control-Allow-Methods', 'GET');
      res.json(response);
    })
  })
  .listen(PORT, () => console.log(`Listening on ${PORT}`)); 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中运行`node index.js`并访问`http://localhost:5000`应该会返回大量数据。

你可以用任何你喜欢的语言做到这一点。你可能已经有了一个托管计划，你可以启动一个 PHP 脚本来做同样的事情。对我来说，我选择 Heroku 是因为他们的免费计划和与 GitHub 的整合。通过更新一个数组让 Heroku 部署它来添加一个提要对我来说很合适。

## Heroku

为了和 Heroku 合作，我需要做几件事:

*   注册免费计划
*   从他们的仪表板创建一个新的 Node.js 应用程序
*   最后，在他们的仪表板上，启用 GitHub 集成，选择我想要的 repo 和分支，并启用自动部署

接下来是一些配置——我需要告诉 Heroku 如何运行这个应用程序。

为了指定运行什么命令(在本例中是`node index.js`)，我在项目的根目录中创建了一个名为`Procfile`的新文件。这个文件包含了一些流程类型，有一些，但是在这个例子中，我们只需要`web`流程类型来启动我们的小 Express 应用程序。因此，我们的`Procfile`看起来就像这样:

```
web: node index.js 
```

Enter fullscreen mode Exit fullscreen mode

最后一部分是创建一个`app.json`文件。这是对我们的应用程序的一种描述。在这种情况下，它是一个名称、描述和要使用的 docker 图像。docker 图像将包含我们的应用程序的环境，在本例中，它是 Node.js.

```
{  "name":  "Feeds App",  "description":  "Returns RSS feeds in JSON",  "image":  "heroku/nodejs"  } 
```

Enter fullscreen mode Exit fullscreen mode

在推送您的更改后，您应该会从仪表板上看到您的应用程序正在部署，当它准备就绪时，单击仪表板右上角的`Open App`会在一个新选项卡中打开它。

在免费计划中，如果 30 分钟内没有流量，应用程序将会进入休眠状态。它会在下一次访问时再次醒来，但在你得到回应之前需要一些时间，因为它会摸索闹钟将其关闭，或者可能会从墙上摔下来。

## 前端

这就是它变得不那么具体的地方。获取数据的方法已经存在，现在的问题是如何显示数据。对我来说，我使用 Vue 并使用 [CodePen 项目](https://codepen.io/pro/projects)托管它。有兴趣可以去[看看源码](https://github.com/tjFogarty/feeds-app-fe)和[看看 demo](https://codepen.io/tjFogarty/project/full/ZPqnVe/) 。我主要使用 React，所以只要有机会我就会使用 Vue。

—

这是一个非常有趣的周末项目，它让我有机会尝试一些我以前不会使用的技术。我把这些项目当成我想学习的有趣技术的游乐场，同时也为自己解决了一个问题。我想要一个可以随意摆弄的 RSS 阅读器，所以现在它出现了。我可能会继续迭代它，但它给我带来了一个有趣的想法:我一生都在为客户构建东西来解决他们的问题。为什么不为自己做点什么，整理一下自己的东西呢？

这最初是在我的博客上发布的。