# 为什么在选择框架或库之前先学习普通 JavaScript 很重要？

> 原文：<https://dev.to/marluanespiritusanto/why-is-important-to-learn-vanilla-javascript-first-before-opting-for-a-framework-or-library--5ffg>

JavaScript，最近一段时间提到的热门词，不是吗？。可以说这是当今最重要的编程语言之一。我们可以用这种语言开发几乎所有东西，从本地移动应用程序到服务器上的复杂应用程序，都有出色的性能结果。它有超过 20 个库和框架是由像谷歌和脸书这样的知名公司开发和维护的。

框架？图书馆？Ups！！第一个不认识的单词来了。如果我们查阅维基百科，我们可以看到**框架** *是一套标准化的概念、实践和标准，专注于特定类型的问题，作为参考，以应对和解决类似性质的新问题*。我们还发现一个**库** *是一组功能实现，用一种编程语言编码，为被调用的功能提供一个定义良好的接口*。有点困惑，对吧？让我们澄清这一点。框架是用来解决某些问题的一套工具、库和最佳实践。这方面的一个例子是 Angular，它旨在促进我们的 SPA web 应用程序开发，并为我们提供了许多工具，以更简单和优化的方式处理 web 元素。虽然该库的目标是在一个简单的 API 中抽象出一系列或多或少复杂的操作，方便调用函数。例如，jQuery 库允许复杂多样的操作，如操纵 DOM、管理事件、创建动画等。并通过一个清晰、简洁的 API 来完成，同时还支持多种浏览器。

"**我应该学习 React、Vue 还是 Angular？**“，”**我把 jQuery 学到最大！**“， **jQuery 老了，我学 Ember** ”，这是很多人在决定学习 JavaScript 时提出的一些问题或者琐碎的主张。**别这样！✋🏼。这是一个严重的错误。没有煮过的意大利面或准备好的酱汁，就不可能做出意大利面，就像没有钻研过香草 JS，就不可能掌握一个框架或一个库。**

框架和库的问题基本上是重量。例如，jQuery 的权重是 250kb(缩小后是 90kb)。起初，这似乎不是一个问题，但如果你的网站每月有 60，000 名访问者，这就成问题了。jQuery 消耗 4gb 的流量。还不错，但是信不信由你，jQuery 和其他 JavaScript 库分量很重。当你意识到使用一个库对你的系统的影响时，是时候考虑你是否真的需要它了。我是不是因为“三无”而超负荷了？。我可以回答。在很多情况下，我做到了。我总是在我所有的项目中使用 jQuery 或 Vue，只是为了选择一个 DOM 元素并更改文本`$(".element").text("hello world";)`或发出 AJAX 请求`$.ajax({url: "[http://mypage.com/api/user"](http://mypage.com/api/user%22)}, method: "POST", data: {}, success: () => { // do something } })`(经典)。但是，对于这种简单的情况和其他稍微复杂一点的情况，这不再是必要的。

## 这里的解决方案清晰而精确:**香草 JavaScript**

自 2015 年以来，JavaScript 不断发展，已经成为一种非常强大和完整的编程语言。我们需要使用库的许多特性已经在本地构建好了。原生 JavaScript 函数非常强大，运行在比 JavaScript 更低的级别上，在分析网站节点并以正确的方式选择它们时提供了更高的速度。我的建议是理解没有典型的框架和库你可以做任何事情。我们应该更多地考虑网络的效率。当你学习普通的 javascript 时，你将拥有更大的能力，你将毫无问题地理解任何框架，最重要的是，你将成为一个完整的开发者。我的朋友总是问我一本学习 javascript 的好书，我的回答总是一样的:

1.  Marijn Haverbeke 雄辩的 JavaScript
2.  [你不知道的 JS 系列丛书(6 本)作者凯尔·辛普森](https://github.com/getify/You-Dont-Know-JS)