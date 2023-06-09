# 我们是如何在网络抓取上失败的

> 原文：<https://dev.to/johnpaulada/how-we-failed-at-web-scraping-1881>

> 也发表在[媒体](https://medium.com/gaplabs-engineering/how-we-failed-at-web-scraping-5db371757352)和 [Boostlog](https://boostlog.io/@johnpaulada/how-we-failed-at-web-scraping-5ac0750f0814730093a2f03d) 上。
> 
> 这就是我们在网络抓取上的失败，以及我们如何克服它——建立链接状态

[![Project Preview](img/19ac37e54caf092371028dc9c593d786.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3JsX1oLA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1400/1%2AEMFD4vzba3vKkSDNGhtrjg%402x.jpeg)

## 想法

上周，我们在 GAPLabs 举行了一次办公室内部黑客马拉松，作为我们每月团队日的一部分。我们要做的是能帮助公司或员工的东西。我的团队想出的大部分想法要么太难了，半天编码都完成不了，要么想法已经存在了。经过大量的头脑风暴，我们选定了一个应用程序，它将成为为公司创建的所有内部项目的目录，这样它们就不会再丢失了。

但是我不满意。一点也不。只是对我的影响不够。我知道我们可以做得更好，但是没有时间去想别的主意了。我快要放弃了。

但我突然想到，如果我什么都想不出来，也许其他人可以。所以我决定在办公室里四处打听，看看他们需要什么工具，并有了一个想法——显然内容团队需要**一个用于检查网页**中链接状态的工具，因为他们使用的工具的许可证即将到期。我认为这是一个很好的主意，也很简单，所以我们开始工作。我们只需要做一些基本的刮擦，对吗？

> 我们只需要从浏览器向给定的 URL 发出一个 HTTP 请求来获取它的 HTML 内容，剩下的就是历史了，对吗？

## 失败

> 唉，命运没有那么仁慈，让我们那么容易地完成任务。

这也不算什么故事。

我一般用 Python 做网页抓取。我想我太天真了，被它的简单给宠坏了。只要把请求和漂亮的库结合起来，你就可以开始了。

显然，用 JavaScript 不能“从浏览器向给定的 URL 发出一个包含 HTML 内容的 HTTP 请求”。使用 fetch API 或普通 AJAX 会抛出一个非描述性的错误，经过进一步研究，我们发现不能发出这样的请求，因为这是一个安全问题。*你知道，那些跨来源/域的请求问题*。

那打乱了我们的计划。那么，现在怎么办？

## 解

我没有使用浏览器，而是检查了服务器是否能够成功地发出请求。我构建了一个快速服务器，并使用 **[Axios](https://github.com/axios/axios)** 来发出请求。成功了！客户端 web 应用程序现在将使用 URL 作为查询参数向 Express 服务器发出请求。Express 服务器还需要一个**Access-Control-Allow-Origin**头，以允许客户端 web 应用程序向其发送请求。你知道，那些跨来源/域的请求问题。

发出请求会从 URL 获取 HTML 内容。下一步是从 HTML 中获取所有的链接。**这是刮削部分**。在开始为此构建正则表达式之前，我搜索了一个可以为我做这件事的库。我发现 **[Cheerio](https://github.com/cheeriojs/cheerio)** 有点适合这个任务。Cheerio 类似于 jQuery，但用于服务器。我在使用类似于 jQuery 的东西，这让我有点毛骨悚然，但是在这种情况下我没有太多的选择。一个快速的`$(‘a’)`达到了目的。拿到所有链接后，我对它们都做了异步请求，检查它们的响应码是否是 **400 及以上**，都是错误码。

然后，生成的信息被发送到客户端 web 应用程序，然后显示出来。我们完事了。

## 建筑

[![Go ahead and laugh 😂 Will replace this later. Maybe.](img/ec1c60418302389243d81eb1e8d55e66.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vsRfSgzG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AHjDW7nC0pN_LJBsQEO0hRQ%402x.jpeg)

## 改进

这个过程需要一段时间，因为它一次检查所有的链接。尽管这是异步完成的，但仍然很多。更好的方法是将所有链接返回到客户端 web 应用程序，并将每个链接异步发送到 Express 服务器，以便用户可以立即看到所有链接，然后在每次链接状态检查完成时更新 web 客户端。这样，当链接被检查时，用户不会一直看着一个空白的白色屏幕。

* * *

> 黑客马拉松是关于想法和当事情出错时的黑客行为——这是必然会发生的。我希望你能从中学到一些东西！我们当然做到了！☺️

### 有问题就在下面评论吧！😂