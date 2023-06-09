# Bugfender 2.0 -幕后

> 原文：<https://dev.to/bugfenderapp/bugfender-20---behind-the-scenes-a7c>

*这篇文章最初发表在我们的[博客](https://bugfender.com/blog/bugfender-2-0-behind-the-scenes/)上。*

[![](img/af050a792489514148d4008580f0bd30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---aHNsr8u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/featured-image.jpg)

作为 [Mobile Jazz](https://mobilejazz.com/) 的开发人员，我们知道在移动应用程序中寻找、复制和修复漏洞是多么令人头疼的事情。几年前，我们厌倦了远程调试移动应用程序，于是开始为自己构建一个解决方案。我们通过创建一种方法来远程访问用户设备的日志记录设施来做到这一点。我们为应用程序日志搭建了一个临时服务器，允许我们跨设备和大洲修复错误。

当时，我们并不知道我们为自己找到的解决方案有一天会发展成为一个独立的可行产品。

自从我们向公众开放 [Bugfender](https://bugfender.com) 以来，它现在被用于超过 9404 个应用程序，安装在超过 4600 万台设备上，每天记录超过 1.2 亿条日志线——这让开发人员可以快速修复令人沮丧的 bug，这些 bug 可能会影响应用程序的成败。

Bugfender 一开始是内部实验，所以我们做的时候没怎么考虑 UI。但是在与我们的[系列计划](https://bugfender.com/pricing/)的真实用户聊天后，我们意识到我们在 Bugfender 架构中有一些限制和缺陷。作为一家自举、完全远程的公司，我们致力于在编写一行代码之前**倾听真实的用户反馈**。

通过几个月的批判性对话和用户反馈，我们知道我们不能满足于现状。

是重新设计的时候了。

[![](img/71f25ed9c9b096265b2e4d1bbb145f63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FrIqMH0S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/problem-solving.jpg)

*挡泥板新设计。*

虽然人们普遍认为渐进的变化比大规模的部署要好，但对于 Bugfender 2.0，我们决定就这么做。我们完全重建了 Bugfender 的 UI，而不是进行增量改进。(别担心，我们已经吸取教训了。更多信息请见下文。)

最初的接口是我们为了功能而创建的 MVP，没有太多的思想去形成。所以它并不是最漂亮的，因为开发人员对内部 UI 并不太敏感——我们只是想要一个运行良好的产品。这是一种迂回的方式(借口？)说我们最初完全跳过了设计过程。

随着平台的发展，客户给了我们添加新功能的好主意，我们在添加功能时没有考虑最有机或最合适的位置。随着我们的进展，我们意识到一些真正关键的新功能需要重新设计，但我们不能一开始就投入太多时间。

但随着我们增加用户，并几乎达到这款产品的盈亏平衡，我们认识到有必要重新设计 Bugfender 的架构，以维持我们对这款产品的期望。对我们来说，完全基于真实的客户需求进行重新设计是至关重要的——而且用户已经发表了意见。

[![](img/f0a94da3816ffb2317ffa7f099df1c73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q8B1MW7P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/1a-new-design-before.jpg)

[![](img/34b18ee5214356b5fc0435077639a83b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L4yCeUYN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/1b-new-design-after.jpg)

*Bugfender 的新旧 UI。*

我们意识到旧的用户界面一次显示了太多的信息。所有这些信息仍然可以访问，但我们已经修改了层次结构，以优先考虑用户希望首先看到的信息。

## 前端改进

> “任何该死的傻瓜都能做出复杂的东西；只有天才才能让事情变得简单。”<cite>——阿尔伯特·爱因斯坦</cite>

我们选择在[角度](https://angular.io/)重建挡泥板。这个框架是我们前端架构的更好选择，因为我们可以更快地**构建新功能**。

Angular 非常适合 Bugfender 这样的数据驱动型应用程序，允许我们构建单页面 web 应用程序。我们这样做有两个主要原因:

1.  更快了。它独立地请求标记和数据，直接在浏览器中呈现页面。这对用户来说更容易和更舒适。
2.  分离前端和后端是一个巨大的架构改进，包括更干净的代码和更少的耦合。这用一个 API 分离了后端功能——我们最终可以为我们的客户开放这个 API。(下面还有更多。)

### 重新设计的目标是什么？

我们的目标是添加使用我们产品的开发人员所要求的顶级功能，所以我们开始构建一个现代化的应用程序，更好地概述应用程序、团队和日志查看器。我们改进了搜索功能。我们还[带来了更符合新网站](https://bugfender.com/blog/how-we-injected-the-new-bugfender-website-with-personality-and-boosted-growth/)的审美。

[![](img/14ba49975584e082b034b34b8d143e04.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FQLYEAdU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/web-app-style.jpg)

*将应用程序嵌入我们的新网站。*

### 更快的搜索

为了改进一般的 UX，我们开始添加更快的**和改进的搜索**。我们增加了**将设备与特定用户**(名字或 id)关联的可能性。这使得在遇到问题时搜索特定用户的设备变得更加容易。虽然这在以前的 UI 中是部分可用的，但我们已经通过使用户名在设备列表中可见而使其更易于使用，例如。

[![](img/c9b16622908b8d63c2e6cda1d3cee59f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TYM7vVF0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/crash-issues.jpg)

我们在 Bugfender 中引入了碰撞报告。

### 崩溃报告

我们添加了**崩溃报告**(反向传输到 BF1)，这是以前我们只关注收集日志时所没有的。我们在日志查看器中集成了崩溃，通过**突出显示不同风格的问题和崩溃**，这样就可以很容易地看到它们。

[![](img/d6f78f245764236143e25c2f4fb70b45.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G8sDxAdp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/2a-new-viewer-before.jpg)

[![](img/f59c371944bcc9cd789e9b14f31ba1f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vlb0ZONQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/2b-new-viewer-after.jpg)

旧的和新的日志浏览器。

### 日志查看器改进

我们**改进了层次结构**,使日志查看器优先于其他辅助功能。我们引入了**定制日志视图**和**改进的日志过滤器**。

改进的日志查看器虽然还在进行中，但将提供**缩进和列，以便日志行可以在表格样式的视图中查看**。

以前日志被分解成会话，但是我们发现会话对于一些人来说是一个难以理解的概念。会话信息可能对一些开发人员有用，所以我们让**会话不那么突出，但仍然可以访问**。我们还可以让**一次性查看单个设备的所有会话**。

[![](img/eaf8746f652d43f16561c4ae9ca235e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JXXIUkGG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/team-page.jpg)

*新设计的团队画面。*

### 更简单的团队

我们还想**改进用户档案和团队档案页面**，这样更容易知道你的团队有什么计划。我们还希望**展示您团队的实际使用情况**，以便您能够判断不同的计划(即更小或更大)是否更符合需求。我们让**更容易邀请新的团队成员**。

### 添加请求的功能

我们增加了这个功能，这样用户就可以**下载. csv 格式的日志**。我们**简化了通知**使其更有意义，**简化了计费**使其更简单明了。

虽然以前大部分功能都是可用的，但我们现在根据用户反馈使其更直观、更容易找到。

## 后端改进

通过分离后端和前端，我们能够**重建日志存储，从而实现更快、更强大的搜索**。我们使用**冗余存储**来实现高可用性和更简单的系统管理。

这些架构变化意味着 Bugfender 现在更具可扩展性:这降低了我们的成本，并将为用户提供更快的产品，而没有停机或缓慢的麻烦。

我们还着眼于未来进行了这些更改:我们已经为自己做好了准备，在不影响用户的情况下部署未来的更改。我们将能够添加或删除更多的服务器实例，以便在任何给定的时间处理更多或更少的用户。

我们还重建了 API 的一些部分，使它变得更轻更快。这将允许我们在未来几天或几个月的某个时候公开使用它。

[![](img/f92edcd026fa9584886ddce73760fe0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mqKgMHmu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/learn.jpg)

## 我们学到了什么

我们同时构建了新的 UI +存储格式变化，这是一种奇特的方式，可以说我们找到了一种高成本的方式，给自己带来了极其复杂的几个月的工作。

我们选择一次部署两个大的变化，这不是我们将来要做的事情。这里要说的是，我们真的应该停止成为超额完成者，在一个给定的产品上，一次只致力于一个“大”任务。

回想起来，我们很遗憾我们没有预先创建一个概览:一个我们想要做出的每一个改变的地图。如果我们做到了这一点，我们就可以把清单分解成独立的、可管理的任务…就像通情达理的人一样。

尽管这种方法可能会花费同样多的时间，甚至更长的时间，但它会带来更少的麻烦。我们认识到，向用户展示你在倾听他们的意见并积极开发产品是有帮助的。当我们两个都做的时候，我们选择的方式意味着用户必须等待很长时间才能看到变化。

如果我们部署了更快、更小的更新，用户将从去年的变化中受益，但相反，他们在过去的一年中几乎看不到明显的改进。(抱歉各位！)但是我们当然希望 BF2 最终是一个超级积极的、受欢迎的惊喜。

关于小型部署优点的最后一点(下次我们会记住):

更慢、更增量的部署会给我们时间，随着时间的推移，以可管理的块从用户那里收集更多的反馈，而现在我们正在经历如此多领域的反馈——这非常有价值——但我们现在必须评估我们的优先级。

[![](img/6f99efd6e807666c8bff3c8fb5e10a8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zGyus_45--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/04/team_1024.jpg)

## 关于我们团队的说明

我们想对来自 Mobile Jazz 的其他团队成员表示感谢，他们帮助构建了新的 UI: Adam、Aleix、Pablo、Luciano 和 Asier 。感谢你们为实现这一目标所付出的惊人努力。

## 从 BF1 转换到 BF2

如果你想知道，这是我们界面转换的路线图:

1.  在早期阶段，个人用户被私下邀请参加测试，以收集对我们正在改进的特定功能的反馈。
2.  然后我们提供了使用新 UI 的可能性，但是旧界面仍然是用户的默认选项。
3.  现在我们已经从新的 UI 中移除了“beta”标签，并使其成为默认界面。如果老用户愿意，他们仍然可以暂时使用旧界面。
4.  我们将在接下来的几周保持 BF1 在线，以帮助以前的用户过渡并学习如何使用新的用户界面。(我们随时欢迎您的聊天！)
5.  好了...然后，我们将永远淘汰旧的接口。

## 结论

谢谢你坚持和我们在一起。最终，我们希望这些改变能让 Bugfender 对我们的用户来说更加可行。重新设计 Bugfender 使我们能够为未来的变化奠定基础，以满足我们不断增长的用户群的需求。

这些巨大的结构变化是我们对 Bugfender 进行长期投资的方式。分离我们的前端和后端将最终降低我们的成本，因为我们将能够比以往任何时候都更快地扩展和构建平台。(当然，当 BF1 和 BF2 同时运行时，我们不会降低成本，但一旦旧的 BF1 退役，我们将看到成本下降。)

我们还有很大的发展空间，包括继续改进日志查看器，以及增加下载不同格式日志的功能。我们将继续给我们的用户更多的选择，我们希望你能继续给我们重要的反馈来实现它。

想看看大家都在大惊小怪些什么吗？[了解更多关于 Bugfender](https://bugfender.com) 的信息。

*这篇文章最初发表在我们的[博客](https://bugfender.com/blog/bugfender-2-0-behind-the-scenes/)上。*