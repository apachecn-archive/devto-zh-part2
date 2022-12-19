# TIL#1:有效地使用 GitHub 应用程序和 jekyll-compose

> 原文：<https://dev.to/pxlpnk/til1-working-efficiently-with-github-apps-and-using-jekyll-compose-2c0j>

我想更多地记录我一生中学到的东西。即使作为一个写了 10 多年代码的人，我们每天都会遇到很多新的东西。

我想与伟大的 dev.to 社区分享其中的一些。

开始了，两件毫不相关的事情:

## GitHub 应用程序

使用 GitHub 应用程序可能会很麻烦。您必须建立一个本地隧道，并使用它们的用户界面触发传出事件。即使一些隧道提供商(这是一个术语吗？)允许你通过他们的接口重放它，这可能是棘手的。

今天我了解到，每个 GitHub 应用程序都有一个日志，记录了所有发出的请求。这些请求包括标题和正文。

您可以在“高级”选项卡下的应用程序设置中找到该请求。

现在我可以从本地文件中用 curl 和无隧道设置来重放东西了。

```
curl -X POST "Content-Type: application/json" -d "@githubwebhookpayload.json" 'http://localhost:8080/webhook' 
```

Enter fullscreen mode Exit fullscreen mode

## 哲基尔-化合物

如果你运行一个由 jekyll 驱动的静态网站，jekyll-compose 会让你的生活更轻松。当你处理草稿时，博客文章和页面的工作变得更加自然。

创建新草稿:

```
$ bundle exec jekyll draft "Today I Learned #1" 
```

Enter fullscreen mode Exit fullscreen mode

这将在`_drafts`文件夹中创建一个新文件。一旦你完成了写作和编辑，你就可以发布草稿:

```
$ bundle exec jekyll publish _drafts/today-i-learned-1.md
Draft _drafts/today-i-learned-1.md was moved to _posts/2018-09-21-today-i-learned-1.md 
```

Enter fullscreen mode Exit fullscreen mode

今天就到这里，每天坚持学习新的东西。🤓

你有办法让事情变得更好吗？让我知道:[你好@an-ti.eu](//mailto:hello@an-ti.eu)