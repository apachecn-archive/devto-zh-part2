# 一种优雅的方式来存档我们贪婪的好奇心

> 原文：<https://dev.to/yoric/an-elegant-way-to-archive-our-voracious-curiosity-29pi>

# 网络好奇心=疯狂标签页

作为开发人员，我们很好奇，最终会浏览互联网上的大量链接。

[![Tabs](img/f9dd71fc84758544af1cbf9972064fd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ks1tnkWk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8gbwyl4yhgp5o9dxs0c.jpg)

有些不值得超过 1 秒的关注，我们马上关闭标签。

有些引起了我们的注意，我们希望以后能再来看，也许它很有趣，或令人感动，或令人窒息，你能说出它的名字。

这就是书签的作用。

然而，想象一下如果你每天标记 20 个链接会发生什么？一年要 7000 个书签，这可不容易。

# 解决方案 1:文本编辑器

我过去常常在文本文件中复制/粘贴链接。我会在我的文本编辑/记事本或 Vim 中创建一个新文件，如下所示:

[![Memo](img/1210b7a76e31113e5ad056712857009c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y7lNFK66--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5bmokn36hhvx7gpm0fn4.jpg)

优点:

*   简单(不需要任何额外的软件)
*   轻量级(不消耗太多内存和 cpu)

缺点:

*   内容很快变得混乱:混合链接、个人笔记、各种主题
*   不容易搜索超过两个月的任何内容。
*   数据丢失:当我们格式化或更换电脑时，我们肯定会备份，但这些文本仍然被埋在备份中，我们再也不会打开，因为我们很忙。

# 解决方案 2:应用和浏览器扩展

从长远来看，我实际上没有尝试过这个选项。
使用类似 [Evernote](https://evernote.com/) 、 [OneNote](https://www.onenote.com) 、
的 app 或者类似 [OneTab](https://www.one-tab.com/) 、 [Tabs Outliner](https://chrome.google.com/webstore/detail/tabs-outliner/eggkanocgddhmamlbiijnphhppkpkmkl) 、 [Toby](https://chrome.google.com/webstore/detail/toby-for-chrome/hddnkoipeenegfoeaoibdmnaalmgkpip) 的扩展。
看起来都很棒，我~~应该~~会给他们更认真的尝试。

highlight 也很有趣，因为你可以高亮显示任何网页上的文本。但是我不喜欢。可能整体上我喜欢简单。

当我写这篇文章的时候，我发现了一些其他看起来很棒的选项。

*   [口袋](https://getpocket.com/explore/must-reads?src=top_navbar)
*   [Wallabag](https://wallabag.org/en)
*   [水银读数器](https://chrome.google.com/webstore/detail/mercury-reader/oknpjjbmpnndlpmnhmekjpocelpnlfdi)
*   [Instapaper](https://www.instapaper.com/)
*   [插接板](https://pinboard.in/tour/)

# 解决方案 3:自托管 Web 应用

为什么是网络应用？听起来很复杂。嗯，一开始我甚至不会去想它。

直到我看到[沙里](http://sebsauvage.net/links/)在行动...这是一个> 10 年前的产品，但它仍然在那里，被作者每天使用，有超过 23000 个链接。
这是一款简约的网络应用，你可以自己托管。它的目的是存储你想要跟踪的链接。

我喜欢它的原因:

*   轻量级:不是怪物。
*   一旦你安装了它，你就拥有了它:你的历史不会出现在第三方服务器上。
*   超级容易安装。`git clone https://github.com/sebsauvage/Shaarli`。完成了。
*   没有数据库:你所有的历史都存储在一个文本文件中。
*   保存链接应该又快又容易。事实也的确如此。
*   接口干净。你可以快速方便地浏览成千上万的链接。
*   您可以在链接上添加注释和标签。
*   “标签云”功能其实很有用。
*   链接可以是“私有”的。

*按下导航条按钮保存当前页面链接后弹出的插图* :
[![popup](img/afe4d4e771d2df4c46756cd45fc1989d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X9UX7fH---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aldzipw6nk0npkf9gtrc.png)

# 终注

对于保存链接这种常见的任务，我们都有自己的个人偏好。这是品味和习惯的问题。
什么最适合你？简单，还是专用工具？
虽然我渴望测试更多的生产力工具，但我倾向于回归基础，使用传统的纯文本。

既然我已经重新发现了这个 Shaarli webapp，我很高兴能在我的(空的)个人网站上尝试一下。

我很想知道你如何处理你的浏览收集和保留。(如果你介意安装你自己的 Shaarli 实例，欢迎分享你的 url，那将是一个非常有用的信息)