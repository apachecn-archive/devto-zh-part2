# 用吉他商店的类比解释 MVC 模式。

> 原文：<https://dev.to/emma_odia/mvc-pattern-explained-using-a-guitar-shop-analogy-15e3>

我有一个朋友，他认为他想了解让应用程序工作的整个抽象过程。这对我来说经常非常好笑，我总是问他是否有兴趣在汽车上做同样的事情！我的意思是谁在换档，并想知道如何换档！那一定很有趣！更不用说当红灯变绿时，所有的人都在咒骂你，而你却在思考点火装置是如何工作的。我最喜欢的一首[诗](https://www.poemhunter.com/poem/stupid-cupid-ep-1/#content)最好地解释了我在这里想要表达的观点。仅仅因为我们现在有牙刷，并不意味着以前没有人用棍子来清洁口腔。多年来，已经部署并重新调整了流程，以使其更好。

MVC 过程与此类似！曾经有一段时间，个人不得不建立自己的服务器，只是为了通过 ftp 共享静态资产，并编写所有的机器级 DevOps 代码！现在，托管您的软件(web 和移动)应用程序很容易，因为组织为您抽象出了大量的开发运维责任。正如已经指出的，我仍然想在这里一针见血的是，一旦任务被抽象出来，它只是为了让生活更容易。因此，MVC 过程通过将 web 应用程序视为作为整个活动的一部分进行通信的独立实体，来确保更快地构建 web 应用程序。我将定义一个 web 应用程序的总体概述，然后使用 MVC 模式解释其结构，并在结尾使用一个实际的吉他商店类比来说明这一点。

假设我们有一个销售吉他的网站应用程序。该网站有例如:
一个登陆页面:主页。静态页面:关于和常见问题。争论数据的一页:联系方式。购物。结账。如果我们要使用 MVC 模式来构建这个网站，我们必须首先定义什么是我们的模型，哪些页面将是简单的视图，哪些页面视图将从模型中返回数据，最后什么逻辑将进入控制器。保持头脑中的形象或者更好的是把它写在纸上。

```
Static VIEWS: Home, About and FAQ pages.

Static VIEWS which returns data: Shop and Checkout pages.

MODEL: Where our guitar data will be saved. 

MODEL we will call SHOP: has_id has_item: guitar {guitar: has_price, has_name}

CONTROLLER: Static Pages controller and Guitar Controller. 
```

Enter fullscreen mode Exit fullscreen mode

当用户登陆主页视图时:静态页面控制器会为他提供一个静态页面。同样的操作当他访问 about 和 Contact 页面时，他由静态页面控制器提供服务(现在您可能已经很快了解到了)。

| 视图 | 控制器 | 模型 |
| --- | --- | --- |
| 主页 | 商店 | 商店 |
| 关于 | 吉他 |  |
| 常见问题解答 |  |  |
| 商店 |  |  |

[![image](img/0f2ff5e8fd8748ca6fd30151f2036188.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3ZFPGFAl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lkn2hn09axeql6zak4pd.jpg)

当用户点击并进入商店页面视图时(很快，你认为这里会发生什么？)他仍然由控制器服务，这次与商店模型通信的吉他控制器根据它们的 id 提供数据库中的所有吉他。当他在选择特定数据时进行结帐时，他被发送到结帐页面，该页面根据数据库中特定吉他的 id 调出该吉他，然后他可以为该吉他付款。

### 吉他店比喻

> 我现在用一个吉他店做例子。

1.  你住在一个城市，并决定追求你对音乐的长期兴趣。你并不是一个糟糕的歌手，为了弥补小小的(可能跑调的)唱歌能力不足，你决定购买一件乐器来为你的歌唱伴奏。你选择的乐器:吉他！
2.  你手头拮据，决定步行去当地市场，那里有几个吉他经销商。
3.  你看到 xyz 商店(主页视图)，走进商店，看到一个更大的卷起横幅(关于页面视图)。
4.  一名销售人员走向你(控制员)，给你一个选项，带你逛商店，看看有没有吉他和它们的价格。
5.  这里的销售员控制器动作可以告诉你关于商店的更多信息(呈现关于视图的静态页面)和/或回答你可能有的所有其他问题(呈现静态页面 FAQ 视图)。
6.  你问了几个问题，然后你们两个继续逛商店，当你走到每一把吉他时，你问名字、品牌和价格。
7.  销售管理员从商店模型中提取所有相关数据，并在每次询问时提供给你。
8.  当你最终决定购买一把吉他时，你通过它的 id 来选择它，然后进行结账，吉他控制器现在提取特定吉他的价格信息，并通过它上面的标签交付它，然后你进行支付。

[![image](img/5572b8fb31482fff13551e0677688664.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CFwMQ2sN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pyc12y547bc49747d953.jpg)

我用 Figma 设计了这个。供图[Unsplash](https://unsplash.com/photos/Jd6eJ-zOE1k)T3】

好吧，如果你读到这里，仍然认为这不酷，我仍然需要在更深的抽象层次上获得概念，只要记住那个不得不购买伴奏乐器来弥补他在音乐上明显失误的家伙！

非常感谢阅读。请在 Twitter 上分享。谢谢大家！