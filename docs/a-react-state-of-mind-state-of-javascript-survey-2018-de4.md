# 反应心态(2018 年 JavaScript 调查状况)

> 原文：<https://dev.to/progresstelerik/a-react-state-of-mind-state-of-javascript-survey-2018-de4>

JavaScript 发展速度如此之快。难怪我们每年都要等待 JavaScript 的[状态调查，以确切了解开发人员的心理状态。今年是我们去年看到的 React 和 friends 受欢迎程度的延续。毫不奇怪，React 再次主导了叙事。](https://stateofjs.com/)

很难跟上所有不同的 JavaScript 包以及它们和使用它们的开发人员之间的关系。很高兴每年都有指标来指导我们，帮助我们在广阔的 JavaScript 领域做出更好的决策

每年，一些著名的图书馆作者都会与一些优秀的网络和数据分析人才聚在一起，在:[StateofJS.com](https://stateofjs.com/)制作一份真正令人惊叹的报告，并且已经这样做了三年。 [2016](http://2016.stateofjs.com/) 、[2017](http://2017.stateofjs.com/)2018。

[![](img/a94cb87755fa5ce0988c66b222ab83ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w2Q60vxb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/MgGFAwu.png)

这个报告可以帮助你做一些事情。这是一个关于 JavaScript 的非常有价值的信息资源，作为一个专业人士，它可以帮助你做决策，或者只是让你对你最喜欢的框架感到高兴或悲伤。

顺便说一句，我应该注意到他们的样本基数逐年显著增长，这应该能更好地代表整个开发人员社区，但似乎仍然存在对 React 的偏见，我在本文的结尾对此进行了介绍。今年，他们接触了 153 个不同国家的 20，000 多名开发者。

## 了解调查参与者

首先引起我注意的是调查中的工资数据。你可以在[人口统计](https://2018.stateofjs.com/demographics/)部分找到这个。

看起来 20 万美元范围内的开发人员数量实际上增加了将近 0.5%。考虑到样本量的增加，我认为这不足以说它在增长，然而，它似乎并没有缩小。我相信，随着 JavaScript 越来越成为用于 web、移动和桌面应用程序开发的最重要的编程语言之一，这一类别将会增长。

[![](img/93b97fa0b55e8d0222bf53f1b660ed80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gfjh7eMM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/GvBtX7d.png)

我们看到多年经验的结果逐年发生有趣的变化。过去，我们看到过类似结果的堆栈溢出调查，所以我们知道这个数据相当不错。但是，如果你也看看年复一年的堆栈溢出数据，我们作为一个 JavaScript 社区倾向于有更多的高级开发人员长期逗留。此图表中的列是多年的经验，我们可以看到所有三年调查的数字。

| | 1 | 1-2 | 2-5 | 5-10 | 10-20 | 20+|
| 2016 年| 2.0% | 10.0% | 29.0% | 30.0% | 23.0% | 5.0% |
| 2017 年| 3.0% | 12.0% | 30.0% | 29.0% | 21.0% | 5.0% |
| 2018 年| 2.4%

我们看到有 0-5 年经验的新 JavaScript 开发人员略有减少，而那些更有经验的人在 JavaScript 领域开创自己的事业。

我还想指出一些关于这个数据的奇怪的事情，比如 2016 年的数字总共等于 99%。2016 年和 2017 年的数字总是四舍五入到最接近的百分比。然而，在 2018 年，我们看到前几列中的数字非常精确，低至百分之几，而在图表的后半部分，它们被四舍五入，再次没有加起来精确到 100%。但它仍然给出了这些数字的年复一年的一个很好的概念，它足够接近得到大的图片。

最明显的一点是，大多数接受调查的 JavaScript 开发者都有 20 年的工作经验。这也让我想知道，我们零到两年的数字是否会因为一些编码训练营在 2017 年和 2018 年逐渐减少而下降。在这一时期，一些大型业务已经关闭，其他业务可能无法快速扩张以填补空缺。这可能与较少的位置和较少的来自训练营的 JavaScript 开发者有关。

查看这篇关于[2018 年编码训练营状态的文章](https://www.linkedin.com/pulse/state-coding-bootcamps-2018-jay-wengrow/)，从一位杰出的行业参与者那里获得一些有用的信息。

### 性别细分

[![](img/f4476b43d0e30f78114ced8cbcb90d30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F-D6b9gr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/IdB9c7u.png)

今年人口统计中的一个新数据点是[性别细分](https://2018.stateofjs.com/demographics/gender-breakdown)。我很高兴我们将有机会在未来几年跟踪这一指标，以衡量我们是否在成为一个更加多元化的团队方面取得了积极的变化。

### 记录丢失的数据点

当我们谈论调查的变化时，我想指出的是，他们拿出的一个数据点是 CSS([CSS 2016](http://2016.stateofjs.com/2016/css/)+[CSS 2017](https://2017.stateofjs.com/2017/css/results))。在我看来，React 社区真的是在挑战像 React 这样的库中 CSS 的极限，所以我发现这些信息很有用。总的来说，我对今年提出的问题感到满意，即使没有 CSS 数据。

## 反应主导着调查结果

让我们不要对此感到害羞，作为开发者，我们对我们在 JavaScript 社区中的地位感到非常高兴。调查中我最喜欢的部分和明显看到 React 着火和不可忽视的力量的地方是[前端框架-概述](https://2018.stateofjs.com/front-end-frameworks/overview/)。

[![](img/b4a174902ee7aaf72431ad0497e80ae6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AtoJQFOF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dkFi1VX.png)

## 前端框架概述

[![](img/64b41b8f11aabbabc26b33aac16c545b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F4Rv8NGc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/HgNS8Un.gif) 

[![](img/01827250e08089b2f7911c1160838a4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iWRUq3jQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mvBwQTZ.png)

## React 开发者领工资了！

从平均水平来看，我们肯定不是业内收入最高的。在需求不高或不受关注的框架中工作是值得的，只要它们曾经足够流行，足以构建重要的大规模应用程序，并且需要维护和新功能。

收入最高的 JavaScript 开发人员(按收入最高的顺序)依次是 [Ember](https://2018.stateofjs.com/front-end-frameworks/ember/) 、 [Polymer](https://2018.stateofjs.com/front-end-frameworks/polymer/) 、 [Preact](https://2018.stateofjs.com/front-end-frameworks/preact/) 和紧随其后的 [React](https://2018.stateofjs.com/front-end-frameworks/react/) ，然后和 [Angular](https://2018.stateofjs.com/front-end-frameworks/angular/) 和 [Vue](https://2018.stateofjs.com/front-end-frameworks/vuejs/) 并驾齐驱。

### 薪资明细概览

[![](img/971450a3c0fd244302ea1787efe725eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1eCcGD8S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/5Cuwy2L.png)

## 开发商❤️为什么会有反应

如果你问我为什么喜欢 React，我会告诉你这是因为它的稳定性、优秀的文档以及我认为的简单的学习曲线。我承认，在 15 年的网络经验和几年使用 [Angular](https://bestofjs.org/projects/angular) 和 [Aurelia](https://bestofjs.org/projects/aurelia) 之后，我开始有所反应。在开始 React 之前，我对大多数基本概念都非常熟悉。它的成长和受欢迎程度都很高，我内心深处知道这比我有时表现出来的更重要。查看 [React -前端框架概述](https://2018.stateofjs.com/front-end-frameworks/react/)页面，了解为什么今年开发者们赞扬和批评 React。

[![](img/9c0e8c8058a90daad9edf60c5ec6c8c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5J6Fz90a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/6tsMLpA.gif) 

[![](img/cd38b9721615cac05c4169cb6dba92d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--akCiacfB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/29mjMF5.png)

这些年来，全世界的开发者对 React 越来越满意。

[![](img/917d96ee8cd76112dc73e8a813c18954.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--1Fdx7tPD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/LqOsAw2.png)

## 参与调查

如果你想参加明年的调查，你可以[注册 JS](https://2018.stateofjs.com/conclusion/) 的状态更新，确保你在他们做下一次调查时收到通知。在 [Progress](https://www.progress.com/company) 我们有许多 React、Angular 和 Vue devs。确保我们都参与并关注调查对社区来说是很有价值的。随着样本量的增加，结果只会变得更能代表其全球社区，这是一件好事。

### 最喜欢方面的反应

[![](img/2415eeb2869646acb6ebf2cd7cadf8d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5xSMqH9---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/uk0pcoh.png)

## 开发商为何反应

我认为开发者不喜欢 React 的原因列表上的一切(你可以在 [React 概述页面](https://2018.stateofjs.com/front-end-frameworks/react#dislikes)上找到)都在 2018[16.3 到 16.7](https://reactjs.org/versions) 版本中得到解决。我真的不觉得 React 有一个陡峭的学习曲线，我知道随着围绕 React 的 API 和工具的改进，体验正在变得更好。我最近在保加利亚的 [DevReach 2018 大会](https://www.youtube.com/watch?v=MskxNm62WMs)上做了一次演讲，直接谈到了我们在 2018 年看到的 React 的改进，以及我认为它们对 React 的未来意味着什么。因此，看到我喜欢的理由也是其他人说他们也喜欢的理由，我感到欣慰。

我也认为 Vue 在[简单学习曲线](https://2018.stateofjs.com/front-end-frameworks/vuejs/likes)中名列第一是有道理的。这是一个小图书馆，它能够从哥哥姐姐们的错误中吸取教训。毫无疑问，Vue 已经盯上了排行榜的榜首。如果作者在它成长的过程中不断做出明智的决定，考虑到 API、添加的和不推荐的特性，它应该不会有问题。

## 分解数据层

这不是巧合 [Redux](https://2018.stateofjs.com/data-layer/redux) 是开发人员的主要数据层选择，我属于没有太多使用 [GraphQL](https://2018.stateofjs.com/data-layer/graphql) 和 [Apollo](https://2018.stateofjs.com/data-layer/apollo/) 但在我 2019 年尝试的事情列表中的一群人。

[![](img/f7525ff0f730c6fe3305a2bf65d37b9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0HKdabC1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/MfdIs89.gif) 

[![](img/119db74df666ae5f971af7d49660126c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PCCeFP6U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/0SPzsYb.png)

## 反应出值得注意的提及

我们都非常保护自己喜欢的图书馆。事实上，如果您使用一个主要的库来构建单页应用程序，这有点像拥有一个最喜欢的运动队。但这可能很危险。认为您对自己的库和其他库有足够的了解是一回事，可以判断您为自己或您的项目做出了正确的决定，但本文的重点是让您思考这样的调查结果和其他调查结果(如 Stack Overflow survey)如何成为工具，帮助您了解应该引起您注意的新技术，并让您了解什么是趋势。

### 连接被库

[![](img/2d89a707b863f58991e45d55effd65b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KgDOu35F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/QvYZHHM.gif)

## 你有什么味道？告诉我你的口味是什么！

我认为 React 比你的一般框架更聪明，并且披着库的外衣做所有的事情，这是导致整个 JavaScript 风格背后的结果的部分原因。

[![](img/47bfcab474209fc69db333646fb0c7fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OTsZsx0y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/N5t8Quz.png)

它的开发者主要依靠 [ES6](https://2018.stateofjs.com/javascript-flavors/es6) 。在今年的成绩和每年的增长中，ES6 和 [TypeScript](https://2018.stateofjs.com/javascript-flavors/typescript) 明显领先。这是个好消息，因为它们也是 React 中使用的最流行的 JS 类型。[流](https://2018.stateofjs.com/javascript-flavors/flow)和[理](https://2018.stateofjs.com/javascript-flavors/reason/)紧随其后。本节的结果清楚地显示了 React devs 的巨大影响。

### 整体 JavaScript 风格

[![](img/033b1b7f739d4e59c99ac5826feeaa09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u8Y_mB_p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/Gsrv7AP.gif) 

[![](img/01827250e08089b2f7911c1160838a4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iWRUq3jQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mvBwQTZ.png)

从这些数据中可以看出，今年最赚钱的可能是非 React 开发人员。不是说 React 里不能用 [Elm](https://2018.stateofjs.com/javascript-flavors/elm) 或者 [ClosureScript](https://2018.stateofjs.com/javascript-flavors/clojurescript) ，只是没那么典型而已。在最大的玩家拉下本杰明和他们喜欢的 JavaScript 风格的类别中，表明他们已经“使用过，会再次使用”。Elm 和 ClosureScript 是那些高薪人士最喜欢的口味。

### 工资明细(红色球)

[![](img/9a671441b4cf70fd43acc4b23aaa7066.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pzdCPj-D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/QEPol8B.png)

不仅那些赚钱最多的人倾向于 Elm 和 ClosureScript，而且将数据翻转到“多年经验”，回答“用过，还会再用”最多的口味也在 5-20 年经验类别中。这表明我们的高级开发人员更喜欢 Elm 和 ClosureScript。

### 多年的经历细目

[![](img/557835641c12b0bd41d0d70c17c08a4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---ZW9prm3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/xTJ41zt.png)

## 用笑话试探

[Jest](https://2018.stateofjs.com/testing/jest) 是 [Create React App 2](https://github.com/facebook/create-react-app) 附带的测试框架，以 39.6%的比例位居测试榜首， [Enzyme](https://2018.stateofjs.com/testing/enzyme) 以 20.3%的比例位居第四，回答“用过，会再用”。查看调查结果中专门测试的页面[。](https://2018.stateofjs.com/testing/overview/)

### 检测结果

[![](img/c09225dc86ea90c6172c8866b0825db1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eN_Jryad--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/VbN2A0p.gif) 

[![](img/3ab10f7eccd9e591c5f2d8fa56c02a26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vj-bc5CE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/17cpi2T.png)

## 桌面和移动上的 JavaScript

就像人们想办法在后端写 JavaScript 一样，他们也会想办法在[桌面和移动](https://2018.stateofjs.com/mobile-and-desktop/overview/)上写。这两个类别结合在一起，在桌面上用 React 写东西最简单的方法是用[电子](https://2018.stateofjs.com/mobile-and-desktop/electron)和移动世界的 [React 原生](https://2018.stateofjs.com/mobile-and-desktop/react-native)。都在名单上名列第一和第二。

### 手机和桌面整体

[![](img/5fbbeead2865f8c6a4573030c46a37db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GNFXAYrn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/ISFAx0Q.gif) 

[![](img/306e987279bcdcb8ce60c7cd0dc6e189.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JBvwW7s9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/KBRybLR.png)

今年很难看到除了后端之外到处都在使用 React。

## 从前到后，如果你跟我在一起你在哪里？

React 是一个前端库，但是很多 React 开发人员都是 [Express](https://2018.stateofjs.com/back-end-frameworks/express) 的用户，这是[后端框架](https://2018.stateofjs.com/back-end-frameworks/overview/)中的第一名，答案是“用过，会再用”

[![](img/bd4e3a03eccc2885318fcc700ea2cc4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nwQuXWVI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/cJtRpzT.gif)

## 在我的雷达上 2019

每年我都会通过这本刊物注意到一些新的东西。在 2016 年，React 确实是我需要关注的事情。作为一个 Angular 和 C#开发人员，它只是在 2016 年之前不在我的雷达上。2017 年，我将被介绍给 React，到 2018 年，我将重新培训自己，并把它作为我选择的前端框架。

2017 年我也开始对[包装管理](https://github.com/yarnpkg/yarn)的[纱](https://bestofjs.org/projects/yarn)有了更多的了解，[玩笑](https://github.com/facebook/jest)和[酵素](https://github.com/airbnb/enzyme)也是我从 JS 状态中了解到的东西。这些是我开始从测试驱动的角度学习 React 的主要原因。

### 其他口味

[![](img/fd047d7a52c3089b64a54bb28b4c5605.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gY7uKc8o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ZImSwJc.png)

2018 年，我注意到一些新的库在类型化 JavaScript 领域看起来很有前途，如 [PureScript](https://bestofjs.org/projects/purescript) ，我很惊讶 [CoffeeScript](https://bestofjs.org/projects/coffeescript) 在[其他库页面](https://2018.stateofjs.com/javascript-flavors/other-libraries/)上排名第二。我认为，尽管 CoffeeScript 在过去几年中被淘汰，并被 Elm 和 ClosureScript 超越，但它仍然在前 10 名中保持强势的事实证明了它是编写 JavaScript 的一种可靠方式，老实说，它为其他库风格铺平了道路。

## 反应相关奖项

即使在作者的[结论中，他们也开玩笑说他们不能“对 React 的日子屈指可数如何做出大的独家报道”，并解释说:**“前端空间都是关于 React 和 Vue.js 的。”**这是我们行业中每个人都应该知道的重要信息。](https://2018.stateofjs.com/conclusion/)

React 比以往任何时候都更强大，JavaScript 及其兄弟前端框架都从今年的调查结果中获得了积极的东西。作为一个社区，我们应该为构建的出色工具而欣喜若狂，这些工具不仅与 React 一起使用，还为整个 JavaScript 世界增加了价值。

在一天结束时，React 有一个很好的表现，我认为我们可以说，颁发的奖项中有四个与 React 有关或有密切联系。

[![](img/4ca78f34cbe06a3d61a407446aa2f491.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--evAhpcGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OJUbEj5.png)

## 落角或有潜在偏差的数据集

对我来说，如果不提及房间里的大象，我很难给予这么多的赞扬。在数据集有偏差的情况下，过分重视调查总是有问题的。毫无疑问，React 是 2018 年最受欢迎的前端框架，然而；我认为 Angular 受到了不利的影响，因为它对社区的抽样可能有很大的偏见。看起来很奇怪的是，TypeScript 做得这么好，而根据这份报告，真正拥抱它的框架似乎有些褪色了。我挑战 Angular 社区中的那些人在会议上谈论这项研究，就像 React 社区那样，指出你认为它的缺陷是什么，并让更多的 Angular 开发人员参与明年的调查。

这是我内心深处的感觉，但我自己并没有做足够的研究来支持这些观点。出于这个原因，我想请你考虑一下 Jeff Delaney 的研究和对该研究的回应。虽然作为 React 开发人员，听到好的东西并巩固我们所知道的东西感觉很好，但是从几个来源听到意见并总是愿意听取相反的意见也是很好的。

[Jeff Delaney 在刚刚发布的 YouTube 视频中提出了一个观点](https://www.youtube.com/watch?v=UnEPBQvkNrg), React 开发人员可能会对数据集做出大量回答，部分原因是作者的背景和调查所来自的社区。他用自己的一些数据和研究证明了自己的说法。不要让这样的研究过多地影响你的观点。永远跟着感觉走。如果你是 Angular 的开发者，不管你认为这个调查对 Angular 说了什么，2019 年都有很多值得期待的。杰夫指出了潜在的[抽样偏差和 Angular](https://youtu.be/UnEPBQvkNrg?t=59) 满意率的缺陷，以及[前端框架结论](https://2018.stateofjs.com/front-end-frameworks/conclusion/)中的陈述:“过去几年的另一个故事是 Angular 的衰落”，以证明这里可能存在一些偏差

我想知道明年的调查是否有可能获得整个开发者社区的更好的样本。我说的不仅仅是有棱角的开发者。我指的是黑人、白人、棕色人种、女性、非美国开发者和非二元性别。我敦促那些没有参与的人注册通知，并确保你的声音被听到，特别是如果你觉得你的团体，社区，性别，国籍等似乎没有在这里得到代表。

这就是我所知道的，请在我们的评论区告诉我你的意见！

> 网站创建者请求并同意发布本次调查的屏幕截图。