# 如何用 netlify 在 hugo 上设置 GNU 特里·普拉切特

> 原文：<https://dev.to/rmhogervorst/how-to-set-up-gnu-terry-pratchett-on-hugo-with-netlify-3f3o>

**TL；博士:**

在这篇文章中，我将向你展示如何在一个静态网站上设置特殊的标题信息，比如 hugo + netlify。Netlify 解释 _headers 文件并将规则应用到您的网站。你只需要制定一个简单的规则，现在你也可以让特里·普拉切特活着！

[![Clacks tower image with title text on top](img/f9a990cf7aa40e77b1407d45686dab16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cKQ7VGHi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/post/2018-07-20-how-to-set-up-gnu-terry-pratchett-on-hugo-with-netlify_files/Clacks-Tower.jpg)

# GNU 特里·普拉切特

2015 年 3 月 12 日我最喜欢的作家之一；特里·普拉切特死了，但是互联网上的人们不愿意让他离开。来自[www.gnuterrypratchett.com](http://www.gnuterrypratchett.com)网站:

> 在特里·普拉切特的 Discworld 系列中，clacks 是一系列信号塔，大致基于电报的概念。由一位名叫罗伯特·迪尔哈特的工匠发明的这种信号塔可以使用标准化代码“以光速”发送信息。其中三个代码特别重要:

```
G: send the message on
    N: do not log the message
    U: turn the message around at the end of the line and send it back again 
```

Enter fullscreen mode Exit fullscreen mode

> 当迪尔哈特的儿子约翰在克拉克斯塔上工作时因事故去世时，迪尔哈特将约翰的名字插在克拉克斯塔顶上，前面有一个“GNU ”,作为永远纪念他儿子的一种方式(或者至少只要克拉克斯塔还立着)。)

# clacks 网络有点像现在的互联网

因为互联网是我们的克拉克版本，一些天才想到在“互联网开销”中添加一个特殊的消息。互联网的开销是:每个服务器发送的响应头。

## 等等，什么？响应头？

那是什么呢？每次你(或者你的浏览器)向一个网站发出请求时，另一端的服务器都会给你一个回应。响应包含网页(希望如此)和一些标题。头给你一些关于服务器的信息，以及服务器是否能响应你的请求。

但是，你可以添加额外的信息到标题中，没有限制。因此，由于互联网看起来有点像磁盘世界中的 clacks (somaphore towers ),我们可以通过将特里·普拉切特添加到每个响应中来让他活着。

## 探测 GNU 特里·普拉切特

我喜欢这个。我的浏览器中有一个扩展，可以读取标题，并告诉我网站是否启用了这个功能。有一个 [chrome 扩展](http://www.gnuterrypratchett.com/#chrome)，一个 [firefox 扩展](http://www.gnuterrypratchett.com/#firefox)，还有一个 [safari 扩展](http://www.gnuterrypratchett.com/#safari)用来检测开销中的 GNU 特里·普拉切特。这是一个小小的扩展，当它检测到什么东西时就会开始闪烁。

例如，荷兰新闻集团 NOS 打开了它！还有《卫报》。

# 在你的 hugo + netlify 部署上打开 GNU 特里·普拉切特

有 wordpress，drupal，apache 的插件。html、django node.js perl 和许多其他 web 框架的脚本。但是雨果还没有。

我通过 netlify 建立和部署我的网站。Netlify 为您提供了指定标题的选项。那么你是怎么做到的呢？

*   (如果您还没有)在静态文件夹中创建一个名为' _headers '的文件

*   将此添加到文件:

```
/*
  X-Clacks-Overhead: "GNU Terry Pratchett" 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，netlify 将向每个响应返回响应头。你完了！

> "只要一个人的名字还被提及，他就没有死。"——走向邮政，第四章序言

## 参考文献

*   [http://www.gnuterrypratchett.com/](http://www.gnuterrypratchett.com/)
*   [链接到如何在 netlify 上设置标题](https://www.netlify.com/docs/headers-and-basic-auth/)
*   我从[http://www . techbritannia . co . uk/2015/03/terry-pratchett-the-good-gnus-assure-hell-never-be-forest/](http://www.techbritannia.co.uk/2015/03/terry-pratchett-the-good-gnus-ensure-hell-never-be-forgotten/)上截取了这张图片