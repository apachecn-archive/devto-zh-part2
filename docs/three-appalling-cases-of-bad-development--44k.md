# 三个骇人听闻的不良发展案例

> 原文：<https://dev.to/piotroxp/three-appalling-cases-of-bad-development--44k>

[![test](img/8f9cb3a5210fc04a68d0943b5ed5b813.png "brain")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RC_mgtq2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.ssl2buy.com/wiki/wp-content/uploads/2015/12/encryption-vs-hashing.png)

# A base64 in base64 in base64

最近我又做了一些 web 开发，这变成了现代开发的一大乐趣，如[渐进式 Web 应用](https://developers.google.com/web/progressive-web-apps/)、[单页应用](https://en.wikipedia.org/wiki/Single-page_application)、作为最新时尚的 Vue.js，以及使我进入现代开发世界的工具，如 npm 和 composer。

* * *

## 如何留下一颗定时炸弹

所以我被一个朋友挑战去调试一个应用程序。由于 NDA 的原因，我不能分享代码，但我会尽我所能描述它。

该网站与经销商搜索有关。当把这个带到桌子上的朋友注意到 PHP 早在 2004 年就已经过时了，他联系我来清理这些东西。

事实证明，不仅服务器软件比 archlinux 的时间机器更老，而且搜索栏也直接向 mysql_*函数发出请求。为了抓住问题的要点，使用了一种特定的 base64_encode 和 decode 模式

1.  解码部分隐藏指令
2.  编码一些其他部分，而解码另一部分
3.  对另一部分进行编码，同时对先前计算的结果进行解码
4.  在联系开发人员时，他说因为我们没有安装 mcrypt 或者在我们的 PHP7 上运行，我们一定是安装了错误的 PHP5。

在 base64 编码/解码中无休止地进行了一个小时后，我放弃了，并决定为后代编写更好的代码。表现得像个小婊子会让你变得像个小婊子——代码就像装满钉子的甜甜圈一样美味。

* * *

## 如何糟糕地管理你的开发人员

我遇到的最有趣的事情是那些“对技术毫不关心”的经理们。

这导致:

1.  开发时间增加(在演示之前，没有考虑必要的开发持续时间)
2.  没有委派的可能性(不懂技术意味着不能正确委派任务，也不能确定你的同事或员工的技能)
3.  没有安全网——技术责任将只落在技术人员身上，而经理可以自豪地说“你只是应该添加 LinkedIn 集成”。
4.  对整体合作缺乏信任。

聘请技术经理。技术型的人更容易学的快。

在做技术项目时，你缺乏数学和逻辑能力不是借口。缺乏技术文化——需要努力。

* * *

### 永不结束的开始

我遇到的最后一个最不方便的问题是不断的“重建”的感觉。大多数开发者和商业人士期望他们的突破性想法就在眼前，你只需要拿这个和那个，瞧，然后你只需要添加这个和那个，然后嘣，我们已经在伊比沙岛，而事实上外面正在下雨，你的生活仍然是狗屎。

开发和研究需要时间。尤其是发展需要时间。与任何人类活动一样，我们会找到第一个可能的模式来处理、衡量结果并优化操作模式。

为了变得擅长某事(例如，开发应用程序)，一个人必须开发应用程序。为了开发一个产品，你需要那些不再需要在开发中证明自己的人——他们不浪费宝贵的时间就能完成任务。

相反，大多数人只是说说而已，没有提出实现目标所需的每日一步(SDSF)。交付应用的是坚持，而不是灵感。绘制第一个模式需要灵感，然后重复你的模型。

* * *

这是我第一次去，希望你喜欢。感谢阅读！