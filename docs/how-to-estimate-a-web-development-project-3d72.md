# 如何评估一个 Web 开发项目

> 原文：<https://dev.to/raddevon/how-to-estimate-a-web-development-project-3d72>

正确估计一个 web 开发项目需要多长时间可以归结为两件事:能够**将项目分成小块**和**从你的经验中了解每个小块需要多长时间**。在这篇文章中，我将分享做这两件事的方法，以及如果你*没有那种经验可以借鉴的话该怎么做。*

[![Stonehenge](img/b3d74ea744e4c09e813bac7122fa0924.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JVYhYeKO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raddevon.com/wp-content/uploads/2018/10/stonehenge.jpg)

## 需要估算时

如果你的账单是基于**时间**的话，估计一个项目需要多长时间是很重要的。你可能会想，如果你按小时(或周或月)计费，你就不需要*用*来估算时间。这个*可能*是真的，但是大多数客户想要了解项目的总成本。

如果你根据项目进行的时间来收取**的固定项目价格，那么这个估价对于确保你至少达到最低时薪，并有望在此基础上获利是至关重要的。如果你估计完成一个项目实际需要一半的时间，这意味着**你现在的时薪是你最初计算的一半**。没有什么比不得不做一个已经远远超出你最初预算的固定费率项目更让人泄气的了。**

如果你基于**值**来给项目定价，你仍然需要至少一个粗略的工作量估算。这可以让你对你的价格做一个现实的检查，以确保你至少达到了你的最低时薪。基于价值的定价*应该*让你大幅提高有效时薪，但是，如果你试图做一个对客户价值低的项目，你可能会以非常低的时薪结束工作。随着经验的积累，你会对**有一种天生的感觉，知道哪些项目你可以承担**，哪些项目对你的客户来说没有价值。

关于项目定价的深入讨论，请查看我的[自由网络开发定价终极指南](https://raddevon.com/articles/the-ultimate-guide-to-pricing-freelance-web-development/)。

## 分解它

大多数你收到的项目都**太大，无法估计**。项目是许多离散任务的集合。与尝试将项目作为一个整体进行评估相比，您更有可能承担其中的任何一项任务，并准确地评估它将花费多长时间。将这些任务分别记录下来，这样你就可以分别评估它们了。

让我们来看一个例子。

### 举例:让我的 WordPress 博客更快更便宜！

一个客户带着一个高流量的 WordPress 网站来找我。网站运行缓慢，托管费用昂贵。客户希望我帮助解决这些问题。我提出让网站适应 [GatsbyJS](https://www.gatsbyjs.org/) 的想法，这是一个基于 React 的静态网站生成器。这将允许客户作为一个静态的单页网站，使其超快，易于托管。客户喜欢这个想法，但是问了一个可怕的问题，**“要花多少钱？”**😱

要回答这个问题，我们首先需要了解这需要多长时间。出于本例的目的，我们将假设我们根据完成所需的时间对项目**进行定价，因为这是评估最重要的场景。让我们把它分成几部分。**

#### 顶级细分

*   让 WordPress 主题适应 GatsbyJS
*   将内容迁移到 GatsbyJS 站点
*   让用户可以轻松地添加内容、构建和部署网站

现在，我们拿了一个项目——把一个 WordPress 站点移到 GatsbyJS——我们把它分成了三部分。尽管如此，这些碎片仍然太大，无法估计。“把一个 WordPress 主题改编成 GatsbyJS 需要多长时间？”我不知道。“方便用户添加内容、构建和部署网站”需要多长时间再说一次，我不知道。这意味着我的作品不够小。让我们再来一遍。

#### 细分主题改编

*   让 WordPress 主题适应 GatsbyJS
    *   查找 WordPress 主题中使用的字体
    *   迁移全局印刷样式
    *   调整割台
    *   调整内容区域
    *   调整页脚
    *   调整侧边栏

在思考如何分解主题改编的过程中，我意识到网站上有些页面没有使用全局页眉、内容区、页脚和侧边栏。这些页面是用构建器自定义构建的。我需要手动构建它们。

*   重新创建自定义的 WordPress 页面

这是分解项目的一个令人愉快的副作用。你会发现项目中你可能忽略的部分。

#### 思维通过内容迁移

*   将内容迁移到 GatsbyJS 站点

当我看着这张照片时，我意识到我需要做一些决定。盖茨比有插件，允许我从许多不同的来源获取内容。我需要决定**哪个来源对这个项目有意义**。

如果我愿意，我可以从现有的 WordPress 数据库中提取，但是这意味着客户端仍然需要在某个地方托管数据库。这将给我的客户在 WordPress 仪表盘上创建内容的能力，这是他们已经习惯了的。我不确定这有多重要。这可能是问客户的一个好问题。

如果我想和 WordPress 彻底决裂，我可以使用不同的 CMS，比如 [Contentful](https://www.contentful.com/) 。尽管如此，我们仍然要为 CMS 支付一笔可观的费用。因为我的客户关心价格，我不想走那条路。在与我的客户讨论这一点时，我了解到**他们对[降价](https://daringfireball.net/projects/markdown/)** 感到满意，所以我会同意这一内容。

我做了一点研究，发现这个决定使得目前的步骤相当简单。我找到了一个工具[，它可以将你的 WordPress 帖子转换成 Markdown](https://github.com/weiran/wordpress-gatsby-migrater) ，并为 Gatsby 创建合适的目录结构。我可能应该测试它以确保它能工作，但是，如果它不能工作，我可以写一个命令行脚本来为我完成转换。如果我想完全安全，我可以估计这个。如果现有的工具工作，那对我来说是一个意外收获！

无论是哪种情况，我都不认为我现在需要进一步分解它。

#### 自动化构建和部署

*   让用户可以轻松地添加内容、构建和部署网站
    *   从降价源生成页面

既然我已经决定降价，我知道我需要这一步来允许我的客户添加新的内容。不过，我不确定我想在构建和部署方面做些什么。最容易开发的是一个简单的命令行脚本，它可以构建和部署，但这并不适用于每个客户端。甚至很多开发人员在命令行都不顺手，更别说普通人类了。😉更多的研究揭示了网络 CMS。这是一个奇特的前端，用于编辑 Git 存储库中的文件。我找到了[一个 Gatsby starter](https://github.com/netlify-templates/gatsby-starter-netlify-cms) ，它包含了我在通过 Netlify CMS 添加新内容后构建和部署网站所需的一切。同样，这项有价值的研究使得这篇文章更加简单明了。

*   来自 Gatsby-starter-netlify-CMS starter 的作品

#### 最后的击穿

*   让 WordPress 主题适应 GatsbyJS
    *   查找 WordPress 主题中使用的字体
    *   迁移全局印刷样式
    *   调整割台
    *   调整内容区域
    *   调整页脚
    *   调整侧边栏
    *   重新创建自定义的 WordPress 页面
*   使用转换器工具将内容迁移到 GatsbyJS 站点
*   让用户可以轻松地添加内容、构建和部署网站
    *   从降价源生成页面
    *   来自 Gatsby-starter-netlify-CMS starter 的作品

现在，我终于有了一些可以准确估计的数据块。

## 估算零件

这里的利害关系是:把这些任务的每一个数字。你将从两个东西中选择一个来得出数字:

1.  你过去做同样或类似事情的经历。如果你只有记忆，那总比什么都没有好…但也不多。理想情况下，你一直在跟踪你的时间，你可以通过历史回顾来知道这个任务在过去实际花了多长时间。
2.  你的最佳猜测。这很可悲，但却是事实，但科学和事实只会让你的估计到此为止。希望你对此有一些基础。即使你还没有完成你正在估算的任务，也许你在过去已经学会做类似的事情，然后真的去做了。做*那个*所花的时间可能和做*这个*所花的时间差不多。

### 但是我没有任何/足够/合适的经验！

当你是新人时，你会发现自己更倾向于猜测而不是经验。**没事**。

你会搞砸的。你会大大低估一些东西，把你在那个项目上的时薪降低到 8 美元/小时。你甚至可以这样做两次或三次(或更多)。

你用所有损失的收入购买的是你下次做更好的估计所需的经验。您正在添加一个数据点，它将影响您的下一个项目评估。请确保你以某种方式抓住它，这样你的钱就不会浪费。

## 你想都不会想的事情

### 客户端通信

客户对沟通的期望是什么？他们想每天和你见面一个小时还是一周一次？他们让你来决定吗？不管怎样，你都要花些时间和客户沟通。请确保在您的评估中考虑到这一时间。

### 修改版本

这个问题可以在你的合同中解决，如果有大范围的修改，需要重新协商条款，但是你可能不得不对几乎每个项目做一些小的修改。一定要考虑到这一点。

### 旅行

大多数网站开发工作不需要你出差，但是有些工作希望你去办公室工作，或者仅仅是签到或者参加会议。这种旅行需要时间，你应该作为项目的一部分来收费。

## 如何入门

如果你是自由职业新手，以下是如何开始的一步一步的方法。

1.  尽可能将项目分解。您希望零件足够小，以便您可以对其进行评估，但您希望避免在这一步花费 3-4 个小时将所有东西分解成细粉。
2.  找出你最不了解的部分。对每一项都做一点研究，直到你对它们需要多长时间有一个稍微好一点的概念。同样，在这里取得平衡。很明显，你可以直接做这个项目来消除所有的猜测，但是这太远了。
3.  尽你最大的努力完成每项任务。我知道你没有多少经验，但利用你所拥有的。如果你遇到一个你完全不知道从哪里开始的任务**，在你自由职业实践的这个阶段，这份工作可能不适合你。告诉客户这个项目不太适合，并推荐其他人，这并不丢人。通过帮助客户找到解决方案，你仍然可以成为英雄。**
4.  将你所有的估计翻倍。 *至少是*的两倍。网络开发者非常乐观。你会发现你认为基于“最坏情况”的估计实际上处于可能结果的中间。你可以通过在你的几项任务中点击*实际*最坏情况来找到答案。将所有事情加倍意味着你不会在这些任务上失去你的衬衫，你在进展顺利的任务上的额外缓冲可能会让你仍然达到你对项目的总体估计。

如果你从这篇文章中有一个收获，那就是作为一个新的开发人员和一个资深的顾问，你会把这个事情搞砸。不要浪费太多的时间试图让它变得完美。

花一两个小时把事情分解，做一些研究。尽可能靠近然后继续前进。记录下实际发生的事情，下次将这些信息输入到你的流程中。最终，你会注意到低估发生的频率**比你接近低估的频率**低得多。这真的是你所能期望的最好的了。

* * *

我在我的新书 [*自由网站开发定价*](https://raddevon.com/freelance-web-development-pricing-rates-estimating-projects-and-turning-a-profit/) 中分享了我所学到的关于定价和评估自由网站项目的一切。买一本来学习我所有的估算、项目定价、证明你的
率和赚更多钱的技巧。💰