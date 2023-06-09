# 参加中西部黑客马拉松与退伍军人联合家庭贷款

> 原文：<https://dev.to/alanmbarr/attending-hack-midwest-hackathon-with-veterans-united-home-loans-12df>

[![The hackathon team](img/05282a5ab069f04906199774fe32d506.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gIR97jxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/www.alanmbarr.com/blimg/team_vu.jpg)

## Hack 中西部黑客马拉松

这个周末，我和一群同事参加了我的第一次黑客马拉松。我们参加两个不同的团队。我的团队 Strike Force Alpha 正在为租房者和出租者创建一个市场，根据他们根据我们的表格回答的问题来匹配他们。在 24 小时内建造一个原型。我所在的团队由一名设计师 Lauren 和四名开发人员组成，我自己、Pearse、Michael 和 Mark。当我们开始竞争时，我们选定了市场理念，而不是我们提出的其他几个理念。我们还决定了如何管理代码，以及使用什么技术来引导。对于我们的栈，我们分别为后端和前端选择了 nodejs 和 angular。Pearse 负责搭建我们的后端 api 服务，Michael 和 Mark 负责设置 angular 并开始使用我们的前端，我负责构建和部署管道以及使用 heroku 支持的 postgres 实例。这条管道很快就派上了用场，在接近尾声时更是如此，因为我们能够快速集成我们的代码，并在网站上看到结果。一旦我们开始纳入第三方 API 的需要，一个活生生的网站成为必要。

## 24 小时是漫长的搅动

我不认为我们需要整个 24 小时，但我们肯定充分利用了它。我们尽最大努力让我们的产品独一无二。SmartHygge a 彻底颠覆了在住房市场将租房者和出租者联系在一起的方式。我们尽最大努力想出体验，并且只内置对这个概念有意义的 API。它们是用于图像的 cloudinary api，用于身份验证的 okta，用于位置和地图放置的 here api，用于 sms 通知和与我们的租赁申请表进行对话的 twilio，以及用于抓取位置位置和与附近位置相关的类别的 foursquare api。

[![SmartHygge](img/6e06248030bcd7e23c2668b419ea373f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--grTMKqBD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/www.alanmbarr.com/blimg/SmartHygge.png)

## 我们获得了 foursquare API 最佳使用奖！

[![The foursquare api winners team missing Lauren](img/45b28bbd86852b600b67c477f3113476.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N-XetbXj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/www.alanmbarr.com/blimg/foursquarewinners.png)

我们都很惊讶我们竟然获得了任何奖项，这是出乎意料的。我觉得在团队中一起工作的经历，在不冒犯对方的情况下紧密合作的经历让我们非常成功。对于这样一个有趣的活动和美好的经历来说，奖品只是锦上添花。我有机会提高我的 postgres 技能，并生成模拟数据来支持我们的前端应用程序。我们起初对这个概念持怀疑态度，但随着我们逐渐融入竞争，我们接受了它，并开始对它进行扩展。特别是我们的设计师 Lauren，她的设计将我们的软件解决方案提升到了更高、更专业的水平。所有团队成员都全力以赴，无休止地工作，创造了一个非常有凝聚力的体验。我们尝试了很多想法，并集成了各种 API，其中一些取得了比其他更多的进展。总的来说，这次经历是一次很棒的团队建设活动，虽然我希望我不必再熬夜 24 小时，但我会考虑在活动中美美地睡上一觉。

如果你对我们的最终结果感到好奇，请随时查看 [SmartHygee](https://hackmidwestfrontend.herokuapp.com/) 我计划将整个网站保留一两周，直到我将其转换为截图。