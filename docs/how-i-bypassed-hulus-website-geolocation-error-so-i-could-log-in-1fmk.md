# 我是如何绕过 Hulu 的网站地理定位错误，从而登录的

> 原文：<https://dev.to/dechamp/how-i-bypassed-hulus-website-geolocation-error-so-i-could-log-in-1fmk>

## 这个问题

所以我每月为 Hulu 付费，他们很棒，我爱他们。然而，他们最近做了一个改变，在 Safari 中给了我一个地理位置错误，不让我登录。它只是坐在欢迎词旁边。

[![Imgur](../Images/9394621f71a25020508d9b373c4f5f86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nf2jMuW_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/maNwffT.png)

我必须使用 Safari，因为我使用 Pipifier 来让我在其余屏幕的顶部观看视频(画中画)。

## 搜索

所以我在这里，清理我的饼干，并仔细检查一切。我不停地点击“接受”给出我的位置。还是一无所获。因此，在这一点上，我做了任何优秀开发人员都会做的事情。我打开我的开发者控制台，开始挖掘。

我首先看到的是`error [geo lib] – 2`...难怪它什么也没做。

[![Imgur](../Images/485ac698362a86dd1492d2ba0944a4b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d5_rJLkG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fxx89cd.png)

接下来，我打开错误并点击代码，这样我就可以看到发生了什么。我有点忽略主要错误，并遵循跟踪堆栈。我看到它发生在设置 cookie 的函数中！

[![Imgur](../Images/804ee26ced08ea53e1506b1eddf076e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xCyAwP2L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ohE9E6P.png)

嘣！我知道这意味着我最有可能找到设置 cookie 的逻辑，并自己设置它。我可以看到它在哪里调用`setCookie`，并且它非常清楚他们用来创建 cookie 字符串的逻辑。

我将省去他们如何设置 cookie 的内部工作，但是 cookie 的名字最终被命名为`geo`。

所以我采用这种逻辑，用它来复制 cookie。我可以看到我需要我的位置，所以我去谷歌地图上查找我的位置，得到我的纬度和经度，然后回到代码。

我非常快速地粘贴值，并通过我的控制台创建 cookie。

## 解

`document.cookie = "geo=" + "".concat(xx.xxxxxx, "&").concat(-xxx.xxxxxx, "&").concat(Date.now())`

然后我回到 Hulu，刷新一下，我就加入了！！！

[![Imgur](../Images/35e93b0884fbccd4a4d139316b9dc789.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t33MjcP---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/HGVwwyL.png)

**注意！！！**
这不是黑客攻击，而是临时修复。我支付全价包月，不滥用我的权限。我不提倡在恶意的庄园中滥用这一点。

完全物有所值！

## 反馈

让我知道你的想法。你做过类似这样的事情吗？

* * *

瓦里马德有限责任公司。

目前的项目有[https://charactergenerator4000.com](https://charactergenerator4000.com)和 [https://coder.exchange](https://coder.exchange) 。请检查它们，并让我们知道你的想法。