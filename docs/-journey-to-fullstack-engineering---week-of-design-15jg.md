# 全栈工程之旅-设计周。

> 原文：<https://dev.to/legobox/-journey-to-fullstack-engineering---week-of-design-15jg>

> 本文是“全栈工程之旅”系列的一部分，这是我作为一个团队构建整个产品的旅程——希望如此。

# 2 周前(回顾)

两周前，我开始尝试创建完整的产品，从产品开发过程的各个方面来看，我的目标是简单地构建有用的应用程序，并鼓励其他工程师在尝试构建应用程序时考虑整个产品，无论他们是一个人团队的一部分。

就像我在上一篇文章中解释的那样，我只是一个好奇的人，碰巧喜欢学习和构建软件的过程，因此，我从来没有真正坚持开发的任何特定方面，因此我喜欢承担我必须承担的任何方面。因此，这里的代码牧马人。从上周的文章中，我决定创造一个产品，所以这里有一个产品是什么的简要概述。我给它取了一个很酷的代号，我喜欢叫它“幽灵”——就像詹姆斯·邦德电影一样。

> 开发人员和团队的部署管理系统，用于管理从存储库到服务器的代码部署。

本质上 spectre 相当于 Laravel 的 envoyer，但是在我们的例子中，我们将处理许多不同栈和项目类型的部署。例如，create-react-app 项目，vue-cli 项目，laravel 项目(显然)，express 项目，随着时间的推移(随着整个事情的成功)，我的目标是将更多的堆栈类型集成到项目中。

# 上周(一路设计)

所以本周的重点是设计和应用程序结构，这对我来说是开发应用程序的第一步。它让我深入了解用户将如何使用产品，以及它将如何执行其指定的操作，对于更多以用户为中心的应用程序，如社交网络和电子商务，这一过程将主要通过侧重于客户分析和用户档案的过程和会话来进行，但在这种情况下，我没有跟进这些，因为我非常清楚我的目标用户是谁——希望推动代码和管理无缝部署的平台和开发人员，没有太多的技术开销。

因此，在所有设置之后，让我们来看看提议的应用程序结构。

> 我通常使用 trello 来管理项目，以创建每周功能的概述，然后使用 todoist 将这些内容放入日常活动和开发会议中，无论是代码还是设计。

## 首页设计。

主页和登录页面通常是真正进行转换的地方，我想创造一些美丽的东西，但同时简单而快速地传递信息，我打算创建一个 gif，虽然它会显示作为页面设置的一部分使用的应用程序的样本，但我还没有，所以目前我解决了插图。虽然我的插画技巧相当生疏，但我认为我在这方面做得不错。通过练习变得更好。这里有一个预览，该项目的大部分设计都可以在 dribbble 上获得

[![](img/00ab64fa607d451b7bb82bd1c1db2b4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ib7_oYEe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C8E47E5F5ED0C422322A743DF57E08596C703485A339139552F57A1B289305C1_1530311137802_Home%2B1.png)

我认为它仍然有点生疏，这里的工作速度很快，但随着项目的进展，主页/登陆页将进行大量的更改，因此我不试图在第一次就把一切都放在那里。

接下来是登录和注册部分。

## 登录并注册

随着登录和注册，我想有一个不同的插图集，并与中央卡设计，我认为没有多少开发人员为导向的产品侧重于这样的设计概念，所以我想尽我所能或任何我已经得到的，而不是在拉下来。

[![](img/1386e4cf815a18a93687117d19ff083a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cjgjFeBc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C8E47E5F5ED0C422322A743DF57E08596C703485A339139552F57A1B289305C1_1530311424313_Signup%2B1.png)

[![](img/5f6ef8c510145bfc8aece031287bcb06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I0fciA6E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C8E47E5F5ED0C422322A743DF57E08596C703485A339139552F57A1B289305C1_1530311441464_Login.png)

在很大程度上，我认为这没问题，但这些部分可能会有一些调整和改进，我希望能得到一些设计师的参与，看看他们能做些什么，希望能得到一些改进这个概念的方法。

在很大程度上，我倾向于一个极简主义的概念，下一个部分是主页仪表板，用户可以在那里获得他们的项目的概述，这些项目由平台管理。

## 仪表盘

该页面将为用户提供他们的项目或项目的快速概述，并帮助他们获得事物的广泛概述，形成设计，我打算仪表板一次处理项目概述，因为项目将处理服务器和协作者，管理部署挂钩，并使用心跳提供一致性检查。

[![](img/d3cfde46f06b28a2071a13e45c5a2cd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PowHrSP_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C8E47E5F5ED0C422322A743DF57E08596C703485A339139552F57A1B289305C1_1530311704430_Dashboard%2B1.png)

虽然我认为这个页面也可能需要一些改进和升级，但在很大程度上，这是一个基本的概述，我会继续改进。
接下来是项目设置。

## 项目设置

[![](img/1566bdeddcb79919b94c7f7adc8af4fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HzJIpuHn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C8E47E5F5ED0C422322A743DF57E08596C703485A339139552F57A1B289305C1_1530312120347_Project%2BSettings%2B-%2BProject%2BDetails.png)

以上是项目的设置页面，这一部分将允许用户能够管理他们的项目细节，并改变有关该项目的某些信息和变量，与设计过程的其余部分相同，它的目的是极简和直奔主题。

[![](img/7a2caa20e0fcf1dc489eb4cb58256bbe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ngNaXI3p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C8E47E5F5ED0C422322A743DF57E08596C703485A339139552F57A1B289305C1_1530312425403_Project%2BSettings%2B-%2BServer%2BDetails.png)

这是该项目的服务器管理系统，这管理项目连接的服务器，它将允许用户能够在需要时添加，删除和更新新的密钥服务器。

[![](img/d762f5539eda914e65a510260eb0cac5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y9hd1RTQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C8E47E5F5ED0C422322A743DF57E08596C703485A339139552F57A1B289305C1_1530312477776_Project%2BSettings%2B-Collborators.png)

如果所有者帐户对项目拥有最高控制权，该部分将管理项目的合作者，拥有删除项目、分配管理员和限制其他用户访问项目的权限。

# 工作进行中

目前，设计过程仍在进行中，我的目标是将所有功能整合到项目中，以便拥有真正可用的产品。与本项目相关的其他设计包括以下部分。

*   档案/账户管理
    *   档案管理
    *   计费和信用管理
*   项目设置
    *   创建新的服务器模式
    *   添加新的协作者模式
*   错误和干杯
*   对的改进
    *   主页设计
    *   仪表板设计

# 结论

目前，我仍在设计阶段，作为一名设计师，更多的是了解用户使用应用程序的体验，并尽可能让他们直观，这样他们在使用应用程序时就不会有太大的学习曲线。

很多这些都可以归结为进入思维模式，目前我主要是作为一个设计师思考，通常，这通常是一个揭示的过程。在很大程度上，虽然我在上午的会议中从事设计工作，但在那些会议中我有更好的精力，在下午晚些时候的会议中专注于基于代码的会议。我的主要设计工具是 Figma，不是倡导者或类似的东西，我只是真的喜欢这个工具，实际上在设计酷的 UI/UX 体验方面给它打了 10 分。

到本周结束时，与配置文件管理相关的所有设计都将完成，这将是对构建解决方案所涉及的开发流程的深入探讨。