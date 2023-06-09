# 逆向工程国泰航空公司的座位选择页面

> 原文：<https://dev.to/henrylim96/reverse-engineering-cathay-pacifics-seat-selection-page-43od>

*本文最初发表于[medium.com](https://medium.com/@limhenry/reverse-engineering-cathay-pacifics-seat-selection-page-89f4d10dd9e8)。*

> ***注**:我不是安全研究员，不具备任何先进的网络安全知识等。*
> 
> *另外，我**没有修改国泰航空网站上的任何数据**，我也没有设法使用这种方法来改变我的座位。此外，使用我在本文中提到的方法，您无法获得任何个人数据。*
> 
> 然而，我可以查看国泰航空所有航班的座位图。T3】
> 
> 另外，这也不是什么新鲜事。你实际上可以使用这个网站做同样的事情:[www.expertflyer.com](http://www.expertflyer.com)。
> 
> ***国泰航空，如果你在读这篇文章，[请在推特上给我发 DM](https://twitter.com/messages/compose?recipient_id=2360382986)。***

### Storytime！

我爱飞行，我绝对爱靠窗的座位(*是的，波音 787 的窗户太神奇了！它超级大，窗户调光就像魔术一样*。

故事是这样的。下周我将乘坐国泰航空公司的飞机去旧金山。)。不幸的是，我的机票票价类型是 S 类(经济舱)，不符合提前预订座位的要求:(

[![](img/26774b6ed36fa9d5a49855d7da873c0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SceyF1QD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/F4uzE0l.png%255D)
[![](img/569070484f64e2e19379f06dc7608a85.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--TeiDGwv6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/svZLJhM.png)

### 我要靠窗的座位！！！

也许我可以查一下还有靠窗的座位吗？所以我去了国泰航空的网站，输入了我的订票信息。然后我点击了“选择座位”按钮，这是我看到的:

[![](img/355030bbfe72ad005c2a6633eb06a312.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FANyfHb9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Cp9chS2.png) 
*不不不不不*

### 所有的座位都没有了？

令人失望的是，如果你没有资格提前申请座位，网站似乎不会向你显示座位的可用性。那对我来说是悲伤的。我现在该怎么办？

### 你好，Chrome DevTools

我还能做什么？当然，我可以打开 Chrome DevTools，看看有没有隐藏的信息。

所以我点击“网络”标签，搜索座位图的 HTTP 请求。瞧啊。这是我的发现:

[![](img/c1011a85b5a5a8e74784b5625779c73a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hs5rWN0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YvpiDjo.png) 
*座位图的 JSON 结果*
[![](img/795c75643a410ac304224e7b8f8eb707.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u45SziJQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/uq0K5xm.png) 
*座位图的 API URL(部分个人数据被隐藏)*

这是让一切变得有趣的部分。如果我将`bookingClass`从`S`(经济舱，不符合座位请求条件)更改为`L`(经济舱，符合座位请求条件)，会怎么样？

所以我打开一个新的 Chrome 标签，编辑参数，粘贴网址，然后点击回车！

### 号错误。消息。

[![](img/55c83c313cb801501bb6ddeb926015fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AzAMpz_S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Al8-zA0uI4PmrkGQ8FC9UUA.png)

它返回完整的座位图。但是这一次，JSON 结果略有不同。

第一次，我可以看到还有一些座位。

我现在看到的是未格式化的 JSON 结果，很难看。我需要一个合适的视角。所以我打开代码编辑器，粘贴 JSON 结果(*由于 CORS 的访问限制*，我不能直接向国泰航空的服务器发出 HTTP 请求)。

在 CSS 和 JavaScript 的帮助下，我终于看到了这个:

[![](img/4a4912d538d2c85ba5b2c1e42dc9e433.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r1p18R5U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/VwjpDZ2.png)

任务完成！可以在这里查看一下演示:[https://button-dew . glitch . me](https://button-dew.glitch.me)，([来源](https://glitch.com/edit/#!/button-dew))。

### 额外:API 的安全性如何？

[![](img/2fba58960c6869f24cd3c71748809b44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kTBR389Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/J3ej3b4.png)

由于某种原因，`currency`、`familyName`、`givenName`、`passengerId`、`passengerType`都不需要。

只要你有`rloc`(预订参考代码)和正确的航班信息，你就能得到座位图。不需要姓和名！

让我们做一些快速的数学运算。由于预订参考代码是 6 位字母和数字的组合，因此代码组合为 2176782336，[波音 777–300e r 经济舱的总座位数为 268 个](https://www.seatguru.com/airlines/Cathay_Pacific_Airways/Cathay_Pacific_Airways_Boeing_777-300ER_C.php)。

因此，您猜测正确预订参考代码的几率是 0.000012311750034340594%。

我猜这很安全？

* * *

### 【更新:2018 年 11 月 2 日】好吧，我错了。这不安全。

[![](img/e4f33272bb38c93817a3f0c167340612.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3SY8FlH2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AGGDZ3DjkigxYEi3QeLZctg.png)

没有。这一点都不好。

只要你有有效的预订参考(`rloc`)，你就能得到**所有国泰航空**航班的全部座位图。

*上面截图是香港到北京的商务舱座位图(CX5900)。当然，我不会乘坐那趟航班。*

* * *

国泰航空，做点什么吧！

* * *

[![](img/c7ed2a5387244df913361c235153124d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1vXKJkMC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AQFhn3NEZJcPvaxqISNUoMg.png) 
*这是他们告诉我的…*

* * *

无论我是否得到靠窗的座位，我都将在下周飞往旧金山参加我的第一次 Chrome 开发峰会。

*(如果你是参加活动的话)*两周后见！呜哇呜！

* * *

[![](img/9d71a5621548f9b0f71df0081bde8b79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--urv1uhoC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/uPNmpFy.jpg) 
*但是不，我不想见到你，卡尔。(那是躲在浓雾后面的金门大桥)。—2017 年 5 月*