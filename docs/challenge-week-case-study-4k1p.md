# 从 UX 到发展

> 原文：<https://dev.to/fmmricardo/challenge-week-case-study-4k1p>

# 挑战周案例分析

[![Working](img/8974869584a36cf4f87f109398b7114f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AyNR6anM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7yv9oajpti4ylexpcgje.jpg)

在当今的技术领域，拥有一个健康的、维护良好的模式库被认为是设计和开发过程的核心部分，可以指导设计师和开发人员获得更好、更一致的结果。这就是为什么在最近的一个项目中，我们开始开发一个模式库。

这个项目始于 Javier 'Simón' Cuello 在新数字学校的大师课。Javier 是 UX 的一名手机设计师，目前居住在巴塞罗纳，曾为 Zara、Telefónica 和雅虎等公司从事应用程序设计项目，也是《设计手机应用程序》的合著者。

# 挑战

作为新数字学校(TNDS)这样一所另类学校的学生，跟踪我们的进步并设定目标以确定每个人的正确道路是非常重要的。为此，我们开发了 Polygon 平台，用于规划和评估我们在学校的学习过程。我和伊姆兰·拉希姆决定开发 Polygon，因为当时它还没有手机版。我们还想对它的工作方式进行一些改进。
我们还认为，在发展我们的设计和开发技能的同时，解决实际问题也很重要。从事这个项目可能会对所有学生产生直接影响。

# 流程

在大师班和挑战周之间，我们遇到了更多的困境。已经考虑开始一个设计系统，我们需要决定我们将如何分割任务，以便它将很容易远程工作。在全球范围内，我们确定了三个不同的阶段:

为多边形设计移动应用程序版本(Android)(初始大师级挑战的一部分)；
流量变化，拥有更好的用户体验；
重新调整从大师班出现的移动应用程序设计，使其适应当前的网络版本及其所有限制；
启动一个模式库；

我们在纸质线框中描绘了我们的应用程序，在漫威应用程序的帮助下，我们能够测试原型，以发现潜在的挑战。

这让我们能够开始增加基于材料设计的线框的保真度。材料设计是我们使用的一个起点。为了保持一致性，我们还了解到这对于坚持用户习惯的交互模式非常重要。这避免了混淆，并在操作系统之间保持相同的用户体验。

我们经历了一些版本，直到达到更好的用户体验。

由于 Polygon 实际上是一个网络平台，我们不得不开始将其视为一个网络应用程序，并相应地调整设计。材料设计的好处是，当涉及到应用程序的环境时，它是通用的，也可以用于 web。

随着 TNDS 的成长和创建更多的 web 项目，它需要一个地方来记录它的视觉模式，并让设计师和开发人员(包括学生)可以访问。考虑到这一点，我们决定探索创建模式库的想法是值得的。

# 模式库

模式库由一组可重用的界面元素组成，这些元素的组织方式允许开发人员查看它们的视觉外观、行为和代码。

对于 TNDS 来说，这个元素集合的目的是保持一致性，允许创建多次出现的元素，从而更容易在未来的项目中重用它们。

我们从识别一些模式开始，比如版式、按钮、卡片、导航条、表单和标签。
[![Buttons](img/dd8bd38ad008dd43e426d376bf850cf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yrliVYaU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/55wfk7fgf6x24vfpptp5.png) 
图 1。图 2 库存
[![Cards](img/ec6bd4e578d27b7bcbe11a0084a76b44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lua6CeAq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nw0gplfkdocsd9p2ec0x.png) 
界面按钮。从网站收集的 UI 卡组件和手机 app 原型
[![UI Components](img/6ff153b92159a840cf80219cffdb863c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c1kajapa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9g8lcp6julnihplmago9.png) 
图 3。从网站和移动应用原型收集的 UI 卡组件

我知道有一些工具可以帮助你避免从头开始构建模式库，我找到了一些像 Atomic 和 Astrum 这样的工具，并对它们进行了测试。之所以选择 Astrum，是因为它是轻量级的，易于包含在项目中，还因为它提供了命令行来管理组件和组。Astrum 的响应也很快，我喜欢定制主题的简单性。
图 4。模式库概述

随着样式表变得越来越大，越来越难维护，CSS 预处理程序(如 Sass)通过保持样式表简洁和允许模块化代码来提供帮助，同时提供一组常规 CSS 中尚不可用的功能。

学习一种新工具需要时间，有时很难找到时间来学习。这个项目对我来说是一个练习 Sass 技巧的机会。在与 Astrum 合作的过程中，我意识到我可以学习和尝试许多概念。通过这样做，我涵盖了原子设计、块元素修改器(BEM)等方法，实现了 Sass 等预处理程序和 CSS grid 等技术。

# 外卖

### 做前计划

我们花了相当多的时间提前计划。这有助于避免项目结束时的挫折。

在开始前做好充分计划的另一个好处是，我有机会发展其他技能，如决策、协作和沟通。

### 沟通和协作至关重要

作为一个团队，最好的合作方式是让每个人都参与到任务中，并在我们进行的过程中测试/调整事情。

我们预先知道，我们为一周设定了很多期望，找到正确的工具和方法是效率、协作和激励的关键。因此，为了让一切顺利进行，我们之间的沟通是必不可少的，特别是考虑到我们是远程工作。

### 原子设计，复杂却强大

我理解拥有设计系统和模式库的价值和潜力。最重要的是，我不仅从这两个概念中学到了很多，尤其是我们在这个过程中使用的技术和方法。

### 完整体验->【UX，UI，开发

我们在 UI Masterclass 的上下文中开始了这个项目，但我们最终做了更多。

我通过研究和试图理解用户来发展我的 UX 设计技巧。用户测试对于改进应用程序、验证我们的假设和提高用户流量非常重要。漫威应用程序是一个有价值的工具，让我们能够测试我们的低保真度原型。
通过 Javier，我了解到了在设计中考虑界面组件的重要性，以及 Figma 和 Sketch 在不同场景中如何成为强大的盟友。
最后，模式库允许我作为前端开发人员发展，同时允许我回顾和应用重要的方法和技术，如 BEM、Sass、CSS Grid。

### 最终消息

这是一个正在进行的项目，所以非常欢迎你分享你的经验。给我留言，告诉我一些我应该考虑的建议和改进。👋
资源

嗯

[原子设计](http://bradfrost.com/blog/post/atomic-web-design/)

[由 Vitaly Friedman 撰写的《粉碎杂志》将模式库提升到一个新的水平](https://www.smashingmagazine.com/taking-pattern-libraries-next-level/)

[伊姆兰·拉希姆简介](https://twitter.com/imran_rahim_pt)