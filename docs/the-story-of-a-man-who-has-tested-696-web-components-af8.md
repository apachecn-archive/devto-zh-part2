# 一个测试了 696 个 Web 组件的人的故事

> 原文：<https://dev.to/thisisbinh/the-story-of-a-man-who-has-tested-696-web-components-af8>

[![](../Images/1d5e309f8bd6e95a8e8b54920b7fb90d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cwenuaYN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vliu7bnu9v85drbhnjhy.png)

###### *图片来源:DZone*

> *“你知道什么是 Web 组件吗？你会如何测试成百上千个呢？”* —萨米·埃克布拉德，我在面试中的主管

这是我探索 web 组件世界之旅的开始。我本人是一名 22 岁的 IT 专业学生，没有任何专业经验。更有趣的是，那次采访是我第一次听到“Web 组件”这个术语

## 从零开始

事情开始时很艰难。我们公司以前没有人做过这项任务，其他人也没有公开做过。没有测试指南，没有格式，什么都没有。这是我列出的范围列表:

*   功能性:有效吗？
*   设计:看起来怎么样？
*   兼容性:它能在所有平台上工作吗？

我首先从 webcomponents.org 手动挑选一些并开始测试。幸运的是，他们提供了 JSFiddle 演示([就像这个](//jsfiddle.net/egu2h4hs/))来预览组件。缺点是它们只有在作者提供示例代码的情况下才可用。尽管如此，这些在线演示还是有点阳光！☀️

为了跟踪记录，我建立了一个电子表格。一切都是手工的，电子表格，写作和测试。它们非常耗时，但我的老板认为这是自动化出现之前的必经之路。

## 自动化开始了。嗯，也不尽然！

随着 webcomponents.org API 的发现，我设法用所有的 web 组件填充我的电子表格，连同它的作者、描述等等。在这段时间里，Python 是我最好的朋友，制作小脚本来处理 API 和电子表格既快又方便。然而，现实情况是，我仍然必须手动测试组件。

Safari Tech Preview、Chrome Canary、Firefox Nightly 和 Opera 是我用来测试兼容性的四个浏览器，主要是因为 Web 组件标准总是在稳定版本获得它们之前首先在那里实现。

## 考验需要耐心

对于有演示的组件，这很容易。你只需要把这个链接复制粘贴到每个浏览器上，看看它能不能工作，对吗？

答案是否定的！

让我们考虑一个人，他制作了 5 个 web 组件，每个组件都是不同的。现在，将这个人乘以 100，你就可以得到数百个有着肥胖手指和打字错误的项目。

对于没有演示的组件，我需要了解它们的用途，它们是如何创建的，以及我应该如何使用它们来编写工作的示例代码。处理数百种代码编写风格简直是一场噩梦。

最后，我甚至不确定是因为元素还是因为我的代码，那个组件不起作用了。

## 结果

随着我越来越熟悉，事情处理得越来越快。我能快速分析出一种成分。

5 个月后，我终于积累了 696 个测试元素(我将在另一篇文章中介绍这些统计数据)。

> “你是怎么做到的？”

嗯，整个过程都是反复试验。体验就是答案。

> “测试了那么多组件后，我感觉如何？”

很累但也很自豪，在做这些之前，我没有任何 web 组件、测试甚至编码的经验。最棒的是。记录是公开的。

> “说真的，什么都是公开的？”

是啊！我测试过的每个工作组件都包含在 [Vaadin 目录](https://vaadin.com/directory)中。他们单独带来了一篇个人评论和一个演示样本，这是我用 CodePen & JSFiddle 做的。

一定要去看看！

[![](../Images/62e863ed6876294dffa073ad94bc975f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BnHbkpMC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/thj4s4i0vhvmujh8fcyl.png)

## 最终字

> "我是世界上测试 web 组件数量最多的人吗？"

也许吧。

> “我现在最了解 Web 组件了吗？”

肯定不是，有很多人理解这个美丽的标准，我只是一个测试者。

> “这个故事的重点是什么？”

这是我一直想在媒体上发表的东西，我一年前开始测试。这次旅行对我来说太神奇了，我希望你能从中学到一些东西。

关于我的旅程或 Web 组件，请随时问我任何问题。感谢阅读！

*   *更多关于 Web 组件的新闻和文章，请在 Twitter 上关注我 [@binhbbbb](https://twitter.com/binhbbbb)*
*   如果你想尝试 Web 组件，请查看系列 [Web 组件星期三](https://vaadin.com/blog?tag=wcw)，在那里我向初学者介绍并解释了易于使用的 Web 组件。