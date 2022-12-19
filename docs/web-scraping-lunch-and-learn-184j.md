# 网络搜集午餐和学习

> 原文：<https://dev.to/bdmorin/web-scraping-lunch-and-learn-184j>

<sub>*转发*:在我工作的地方，我们有一种叫做午餐的东西，在那里，公司的人可以和其他人谈论一些事情。有时是客户概述，有时是水肺潜水，有时只是为了介绍新人。我做了一个关于网络抓取以及它如何帮助你的日常业务、个人或其他工作的演讲。这是我做的演示，单独看可能没什么意义，但我想分享一下。链接到原始的[演示文稿](https://docs.google.com/document/d/e/2PACX-1vS9wX_LaXUCuBD_P_PhCxzMiWduKpKhqhFIHdqDzs_eWpyoVzxk3qsJe4NqqjhF1TNSawjge_bi5sv1/pub)。</sub>

## 

# 网页抓取 L & L

## 我拿结构化数据换 100 个 Alex。

### 概述

web 抓取或数据挖掘的目的是将 web 数据转换成可以用不同格式处理的结构化数据。围绕数据挖掘、web 自动化和 web 抓取有一个巨大的产业。我整理了一个示例方法，说明如何在遇到需要构造自己的数据时进行简单的抓取。

#### 演示工具

这些是我在演示过程中使用的工具
[https://data-miner.io/](https://data-miner.io/)(chrome 扩展)
[https://data-miner.io/quick-guides/menu](https://data-miner.io/quick-guides/menu)
[https://sheets.google.com](https://sheets.google.com)import XML()和 importhtml()函数

#### 我们刮来的网站:

*   [https://vigilante.pw/](https://vigilante.pw/)
*   [https://broadbandnow.com/Cable](https://broadbandnow.com/Cable)
*   [https://www.npmjs.com/](https://www.npmjs.com/)
*   [https://www . LinkedIn . com/my network/invite-connect/connections/](https://www.linkedin.com/mynetwork/invite-connect/connections/)
*   [https://admin.google.com](https://admin.google.com)

### 挑战

为了使用 dataminer 抓取网站，你可以通过观看教程视频来节省大量时间。它向您展示了如何在基本情况下有效地使用该工具。由于您需要更高级的特性，您可能需要学习 CSS 选择器、jquery 选择器或 xpath 选择器。此外，对于更复杂的抓取任务，您可能需要 data-miner.io 的商业帐户，或者迁移到 scrapy/portia 等开源框架。

#### Javascript

网络抓取的最大挑战之一是处理 Javascript。使用 Angular、Vue、React 的网站不会很好地呈现典型的基于请求的 web scraper。Data Miner 已经很好地处理了基本用例，因为它使用浏览器后期渲染的 HTML 来抓取。抓取库需要首先通过无头浏览器或其他选项处理 javascript。商业上有代理加载 HTML 的选项，它会在解析器分析 HTML 之前预先呈现站点，还有像 Puppeteer 这样的项目，它使您能够拥有一个本地运行的无头 chrome 浏览器(与 phantomjs/capserjs 不同)。

scrapy 生态系统有一个名为 Splash 的伟大项目，它是一个 api 驱动的 dockerized headless web 浏览器。您的蜘蛛只需向 api 发出请求，它就会处理渲染。Splash 在许多情况下非常有用，因为自动抓取器需要处理需要 javascript 的登录页面。

### 刺儿头/波西亚

Scrapy 和 Portia 是一个开源的商业服务，如果你需要的话。Scrapy 是一个 python 框架(基于 Django ),用于部署网络爬虫、蜘蛛和爬虫。Scrapy 易于使用和开始，并且如果需要的话可以扩展到非常高级。Portia 是一个开源应用程序，它为开发刮痧食谱创建了一个可视化的方法。Portia 可以是自托管的，也可以作为服务托管。我通过 docker 运行了一个本地 Portia 实例，虽然它很简洁，但是有问题，而且经常崩溃。这对新用户来说是令人沮丧的。
[https://github.com/scrapinghub/portia](https://github.com/scrapinghub/portia)
[https://github.com/scrapy/scrapy](https://github.com/scrapy/scrapy)
[https://github.com/scrapinghub/learn.scrapinghub.com](https://github.com/scrapinghub/learn.scrapinghub.com)
[https://github.com/scrapy-plugins/scrapy-splash](https://github.com/scrapy-plugins/scrapy-splash)
[https://django-dynamic-scraper.readthedocs.io/en/latest/](https://django-dynamic-scraper.readthedocs.io/en/latest/)

### 无框架 Python

如果你想从头开始写一个抓取机器人，并且没有框架开销，BeautifulSoup4 和 Requests 是一个不错的选择。您可以用大约 20 行代码开发多级刮刀，但是您需要提前了解库和方法。BS4 和 Requests 一样有很好的文档，几乎任何 python 初学者都可以开始使用它们。还有一个非常方便的 python 库，它可以自动从页面(如报纸文章内容)中提取核心内容，名为 Newpaper3k，如果你想为 AI 或 ML 等任务提取大量内容，这是一个很好的模块，可以帮助你专注于不抓取，而是如何处理你抓取的内容。
[https://www.crummy.com/software/BeautifulSoup/bs4/doc/](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
[http://docs.python-requests.org/en/master/](http://docs.python-requests.org/en/master/)
[https://newspaper.readthedocs.io/en/latest/](https://newspaper.readthedocs.io/en/latest/)

### 节点刮

我没有做过太多关于 Node 的研究，但是我读过很多关于它的文章。对我来说，最大的障碍是任何不使用 promises 的请求库都很容易被挂起。我试过一些，但是我真的很喜欢用 Python/Jupyter 开发。这里有一些在 Node 中启动 webscraping 的资源。
框架:[https://expressjs.com/](https://expressjs.com/)T3】请求库:[https://github.com/mikeal/request](https://github.com/mikeal/request)或[https://github.com/axios/axios](https://github.com/axios/axios)T8】HTML 解析器:[https://github.com/MatthewMueller/cheerio](https://github.com/MatthewMueller/cheerio)或[https://github.com/jsdom/jsdom](https://github.com/jsdom/jsdom)

### 命令行

有时，您只想直接从命令行获取数据。有两个工具可以让这变得非常简单: [pup](https://github.com/ericchiang/pup) 和 [jq](https://stedolan.github.io/jq/) 。
举例:

```
curl -s "https://vigilante.pw/" \
| pup 'table tr json{}' \
| jq ' .[] | {"entries": .children[0].text, "database": .children[1].text, "hashing": .children[2].text, "category": .children[3].text, "date": .children[4].text, "acknowledged": .children[5].text }' | head -40

{
  "entries": "34,368",
  "database": "000webhost.com Forum",
  "hashing": "vB",
  "category": "Hosting",
  "date": "2015-10",
  "acknowledged": null
}
{
  "entries": "632,595",
  "database": "000webhost.com Mailbox",
  "hashing": "plaintext",
  "category": "Hosting",
  "date": "2015-10",
  "acknowledged": null
}
{
  "entries": "15,311,565",
  "database": "000webhost.com Main",
  "hashing": "plaintext",
  "category": "Hosting",
  "date": "2015-10",
  "acknowledged": null
}
{
  "entries": "5,344",
  "database": "007.no",
  "hashing": "SHA-1 *MISSING SALTS*",
  "category": "Gaming",
  "date": null,
  "acknowledged": null
} 
```

Enter fullscreen mode Exit fullscreen mode

这个例子使用了我们之前看过的 vigilante.pw 网站。在命令行上，您使用`curl`作为请求者，`pup`只提取表中的行，并将它们转换成 json，然后`jq`将 json 处理成可在任何其他 web 应用程序中使用的数据集。jq 可以进一步删除数字中的逗号，并在需要时规范化其他文本。

### 奖励回合

把这个放在谷歌表格里。

```
=IMPORTHTML("https://vigilante.pw/","table",1) 
```

Enter fullscreen mode Exit fullscreen mode

您几乎可以将任何喜欢的 xpath 导入到 google sheets 中，使您能够创建 web 内容的定制仪表板。

<sub>照片由[麦克·琼斯](https://unsplash.com/photos/nZcMWOKAJrY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄 T3】</sub>