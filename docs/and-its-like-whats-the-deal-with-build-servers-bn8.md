# 这就像…构建服务器是怎么回事？

> 原文：<https://dev.to/kritner/and-its-like-whats-the-deal-with-build-servers-bn8>

我在我的博客上将我的 RSS 订阅改成了 medium，但是这些还是会弹出来...所以转贴(虽然我可能改了一些内容)

<figure>[![](../Images/5afa29c2413aecfae27dc127c5709b7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GkqSsNj5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AfYFYwcgI01MK_OXW)

<figcaption>【img IX 采矿钻机内部白色和灰色房间】由 [imgix](https://unsplash.com/@imgix?utm_source=medium&utm_medium=referral) 上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

我能想到的解释构建服务器的最简单的方法是想象为每次代码签入雇佣一个全新的开发人员，给他们明确的指令来完成构建，然后让他们去做。也许这需要一点解释——构建服务器的想法是提供一组可重复的指令/步骤来从头到尾完成应用程序的构建，而不需要签入代码的开发人员的额外输入。

作为一个没有构建服务器的开发人员，您可能会遇到这样的情况:您的项目在本地构建得非常好，但是当另一个开发人员试图构建您的最新版本时，您就会遇到编译错误。不幸的是，对于我从源代码控制中拉出的每个项目，我都经历过这样的情况(在没有使用构建服务器的情况下)——这并不是说我责怪所涉及的组织、个人或项目——如果你不知道得更好，你就不知道得更好。

一旦你了解了构建服务器，我觉得尽可能实现一个是很重要的，因为它们相对容易配置，一旦完成，开发者可以节省大量的时间。

我只看到两个不使用构建服务器的理由…其中一个更多的是借口而不是理由。第一个原因如上所述，如果您不知道构建服务器过程，那么您可能不会有一个。第二个原因(借口)——会认为设置它“太难”或“不值得”。如果构建服务器太难设置，这可能意味着您的手动构建过程非常复杂，并且从构建服务器中获得的好处可能比简单的应用程序多。如果第一次从源代码控制中抓取一个项目给你带来了 10-15 个以上的错误，这些错误可能需要 5 分钟到几个小时才能解决——并且涉及到几个开发人员——那么你真的需要考虑为了修复这些错误需要改变什么。

是否使用了需要添加到源代码中的外部库？构建所需的开发人员机器上是否缺少几个 SDK？我是否在签入中遗漏了不允许下一个开发人员构建的东西？当在本地建造时，所有这些问题都很难推断出来。有了构建服务器，它就像一个独立的开发人员第一次在一个项目上工作，每次都是如此。

如果某个新的依赖项被添加到项目中，并且在签入时被遗漏，构建服务器将立即报告失败，并且开发人员(可能)会得到这样的通知，并且可以采取措施进行纠正。如果没有构建服务器，为什么一个项目突然不能构建，或者为什么一个项目第一次就不能构建，这可能是一个谜。

那么为什么你还没有一个构建服务器呢？

<figure>[![](../Images/f7a205bd14444c9ef81451213396a4e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ELd-YhCA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A4wgXFNU3ENsEoktf) 

<figcaption>【矩阵电影剧照】由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

其他构建服务器包括(但不限于):

竹子([https://www.atlassian.com/software/bamboo](https://www.atlassian.com/software/bamboo))

团队城市([https://www.jetbrains.com/teamcity/](https://www.jetbrains.com/teamcity/))

蚂蚁([http://ant.apache.org/](http://ant.apache.org/))

咕噜([http://gruntjs.com/](http://gruntjs.com/))

等等

拥有一个构建服务器可以为您和/或您的组织带来很多好处，但是在我看来，最大的好处之一是实现自动化部署的能力。一旦构建完成，项目的各个部分就可以自动部署到下一个环境中。

*原载于 2015 年 1 月 21 日 kritner.blogspot.com***。**