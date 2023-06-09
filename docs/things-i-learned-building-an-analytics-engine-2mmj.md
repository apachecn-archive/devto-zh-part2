# 我在构建分析引擎时学到的东西

> 原文：<https://dev.to/dougblackjr/things-i-learned-building-an-analytics-engine-2mmj>

天啊，我太激动了。这个副业已经有一段时间了。

我刚刚发布了 Engauge Analytics([https://engaugeanalytics.com/](https://engaugeanalytics.com/))，这是一个网络分析引擎，可以让你从你的网站上获得有意义的数据，而不用像其他一些大型实体一样跟踪你的用户*咳咳谷歌和脸书咳咳*。

此外，Engauge 的付费层允许您对您的网站进行全面、自动化的 SEO 评估，以及机器学习驱动的内容评估，帮助您在访问之前了解您的受众对您的内容的反应。

这是我做过的最古怪的东西，我为此兴奋不已。但是做起来很痛苦。
[![Much fire, many hurt](img/504d76df07c8209f3deb1aadfbf1bef7.png)T3】](https://i.giphy.com/media/ZylxpySpaBlhESsNGL/giphy.gif)

以下是我在建造它时学到的一些经验。

**在建造它之前，想想你在创造什么**
[![My first uh-oh](img/92172f4618b597814aab690b99e8a7ae.png)](https://i.giphy.com/media/457NTDOuMmDfO/giphy.gif)

随着分析引擎的运行和测试，我深入到应用程序的用户界面的构建中。我的一个朋友给我打了一个电话，他的一个朋友在一家大公司做产品。

我从头到尾向他介绍了这个产品，使用了“专有”这样的花哨词汇。

他的第一个问题是:“那么你为谁做这个？”

问得好。我真的没有想过这个问题。

所以，副业既有趣又有挑战性。我从前面学到的最重要的一课是:如果你要推销它，想想你要把它推销给谁。

**征求反馈**
[![Let's escalate quickly!](img/7c9473fb9f3859a6f8885cfb056125aa.png)](https://i.giphy.com/media/MF3fDJIFZ0ORO/giphy.gif) 
在发布前两周，我邀请了多位用户在 alpha 中测试并给出反馈。反馈非常棒，并引出了许多我错过的常识性功能。

有些话有点难以接受。这是我的孩子，我从零开始孵化它，向人们询问如何抚养这个孩子的反馈有时是痛苦的。

但是，每一个建议都带来了发展应用的机会。并不是所有的建议都得到了实施，但是一些建议告诉我，如果没有这些建议，这个应用将一事无成。

**数据得到大快**
[![I mean, biiiiig](img/4dc954525effdbe6a9cc210dffc68319.png)](https://i.giphy.com/media/3o6Zt83sUrmHPYdgkM/giphy.gif) 
所以，app 设置的方式，我就知道数据库会得到大快。我只是没意识到有多大。

它对阿尔法用户来说太大了，以至于整个系统都崩溃了。我必须快速扩展，并考虑以更大的方式扩展。

扩展服务器和数据库很容易，但我仍然对此不太满意。对于一个大规模的、快速查询的关系数据库有什么反馈吗？

**享受旅程**
[![It'll work out](img/2d3b632c5ed3fc3ad181644c1a60d32f.png)](https://i.giphy.com/media/z9AUvhAEiXOqA/giphy.gif) 
也许你也来过这里:“这边项目做完了，现在下一步是什么？让我们建点别的吧！一个观察暗物质的空间监视器 API，利用....webpack！”

这似乎是很长一段时间以来(也许是有史以来)第一个没有让我立即继续前进的兼职项目。这是建立起来的，我想看到它像疯了一样成长、成功和扩展。

我不得不承认，我还在不断学习。我会张贴更多的，也许是一些操作方法，关于我是如何建立这些的。但是，我知道在这方面还有我没有触及的增长领域。我有版权吗？申请算法专利？扩展到不同的数据库结构？多租户？

是时候继续学习了！