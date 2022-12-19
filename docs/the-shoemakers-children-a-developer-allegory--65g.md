# 鞋匠的孩子:一个开发者的寓言

> 原文：<https://dev.to/joncassdev/the-shoemakers-children-a-developer-allegory--65g>

这是一个简单的计划:给我的个人网站改头换面，并写一篇关于这个过程的博文。

在了解 UI 框架后不久，我就建立了自己的网站，虽然我的基于布尔玛的网站比一些网站更好，但我并不喜欢它。下面是“之前”的图片:
[!["before" image](../Images/ca540797742826a1243f5086931c4575.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z5Jt-88i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sq9u5ca4nqd8mqz0diy5.png)

此外，我有点不好意思地承认，尽管我热爱 [CI/CD](https://en.wikipedia.org/wiki/CI/CD) ，但我个人页面的“发布”过程是`ssh`进入服务器并执行`git pull`。不完全是最先进的。

我开始研究静态站点框架，并想也许我会用 [Gatsby](https://www.gatsbyjs.org/) 来构建我的站点，并用 [Netlify](https://www.netlify.com/) 来部署它。有了盖茨比，我甚至可以直接在自己的网站上写博客了！毫无疑问，这是开发我的页面的现代最佳实践方式。然后我看了[的这个帖子](https://dev.to/flexdinesh/create-your-developer-landing-page-with-github-pages---42jk)，作者是 [@flexdinesh](https://dev.to/flexdinesh) ，我的计划崩溃了。他的帖子展示了一个他开源的[优雅的开发者登陆页面](https://github.com/flexdinesh/dev-landing-page)，以及在 [GitHub 页面](https://pages.github.com)上托管它的说明。我最初不愿意使用他的项目，因为我觉得我应该建立自己的页面，但最终我意识到它正是我所需要的，并将帮助我编写一个比“这是我如何建立我的网站”更有趣的帖子。所以我向你们展示:一个开发者的寓言。

如果你不熟悉[“鞋匠的孩子光着脚走”](https://en.wiktionary.org/wiki/the_shoemaker%27s_children_go_barefoot)，这是一个表达，意思是人们在个人生活中经常忘记专业知识。我的个人网站无疑就是这种情况——我忽略了所有的开发最佳实践。我将在这篇文章中谈论的四个问题是:

*   持续部署(CD)
*   保持简单，笨蛋(吻)
*   “不是这里发明的”综合症(NIH)
*   不要重复自己(干)

## 连续部署

在连续部署中，每次您向项目的主分支提交代码时，都会运行一套测试。如果所有测试都通过了，新的构建就可以部署了。这允许您非常快速地获得关于变更的反馈，并使发布代码(这通常是一项有风险的工作)成为一件小事。给一个项目添加 CD 的方法有很多，包括[特拉维斯](https://travis-ci.org/)、[圈](https://circleci.com/)、[詹金斯](https://jenkins.io/)和 Netlify。

如上所述，我最初想重做我的网站的原因之一是为了改善我的“发布流程”。我的旧流程有几个问题。第一个问题是它很麻烦:每次我提交对 GitHub 库的更改时，我都必须执行额外的步骤来将这些更改放到我的“生产环境”中。第二个问题是，这些额外的步骤包括一个`git pull`，这可能会产生意想不到的后果，正如这里的[所记录的](https://grimoire.ca/git/stop-using-git-pull-to-deploy)。我想安装一个连续的部署管道，使过程更简单，更不容易出错。

## 保持简单，愚蠢

虽然 Netlify(和其他 CI/CD 管道工具)肯定可以处理我的简单静态站点需求，但它们有些过头了。我没有要运行的测试套件，也没有复杂的资产或构建过程。我只是需要我的代码变化反映在我的网站上，正如我从 [@flexdinesh](https://dev.to/flexdinesh) 的帖子中了解到的，这正是 GitHub pages 所做的。

在措辞上有点刻薄， [KISS](https://en.wikipedia.org/wiki/KISS_principle) (保持简单，愚蠢)恳求我们避免复杂。它也在极限编程中表现为 [YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it) (你不会需要它)，在设计中引用安东尼·德·圣·埃克苏佩里的一句话:“最终达到完美不是当不再有任何东西可以添加时，而是当不再有任何东西可以拿走时”。通过保持代码、过程和用户界面的简单，隐藏错误的地方更少了，用户和开发人员的困惑也更少了。

## “未发明于此”综合症

当我第一次看到 [@flexdinesh](https://dev.to/flexdinesh) 的帖子时，我很兴奋地了解到 GitHub 页面，我认为他的登录页面看起来棒极了。尽管如此，我为为什么不能使用他的框架找了借口:列出和链接我的项目不容易，我不能合并博客，我没有地方放关于我自己的信息，我的“我如何建立一个投资组合网站”博客帖子与他的相比是多余的。我表现出[“不是这里发明的”综合症](https://en.wikipedia.org/wiki/Not_invented_here)。

不愿意使用现有外部实现的原因有很多:成本、骄傲、对未知的恐惧。然而，NIH 完全反对开源文化。在开源中，我们可以在彼此的解决方案的基础上进行构建，最终创造出比我们任何人单独完成的都要伟大的东西，而不是强迫每个开发人员或团队重新发明轮子。一旦我在自己身上发现了 NIH，我就能够与之抗争。

## 不重复自己

有了 Dinesh 的项目，我放弃了在我的网站上发布博客文章、展示我参与过的具体项目和应用程序，并在那里列出资格和职业经历的计划。我想得越多，就越觉得有道理。我已经有了 dev.to 来发布博客，GitHub 来展示我的项目，LinkedIn 和一份列出资历和职业经历的简历。为什么我要在我的网站上复制所有这些内容，要求我在这些内容发生变化时在多个地方进行更新？我还了解到 GitHub 允许你选择你想在你的 GitHub 个人资料上展示的项目(“定制你的固定库”)，这为我敲定了这笔交易。

意识到我一直在复制信息让我想起了编程中的 [DRY 原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。DRY 消除了在多个位置执行单次更新的需要，从而节省了时间并防止了错误。当然，干燥可能会(以我的经验来看，经常是)走得太远。为了删除重复的代码，开发人员通常会创建一个抽象层，但是如果重复更多的是巧合而不是等价，这可能是危险的。你可以在 Sandi Metz 的帖子[中读到更多，复制远比错误的抽象](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction?duplication)便宜。

## 总结起来

我对最终产品非常满意，也可在[www . Jon cass . com:](http://www.joncass.com:)
[!["after" picture](../Images/78fe97fdb0adb00b3a4d4609e3186db2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lGyuu5WW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2o1b3x0denuqtxy94bn8.png)查看

正如鞋匠的儿童格言所说，我的个人项目经常得不到我在工作中应用的爱和严谨。虽然这对于一些项目来说很好，特别是在我可能不会继续进行的早期，但我希望我的“web presence”网站能有一点额外的关注。如果容易的话，我更愿意保持更新(谢谢 CD！).我认为我的新的简单(大声吻)和整洁(漂亮和干燥)的页面让我把我最好的脚放在网络上，即使它不是在这里发明的:)。

有问题或意见吗？把它们留在下面！

*非常感谢 [@flexdinesh](https://dev.to/flexdinesh) 开源他的开发登陆页面项目(并撰写相关文章)。*