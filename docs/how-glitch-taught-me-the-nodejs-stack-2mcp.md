# Glitch 是如何教会我 Node.js 堆栈的

> 原文：<https://dev.to/stegriff/how-glitch-taught-me-the-nodejs-stack-2mcp>

在工作中，我是一个完整的堆栈。Net 开发者。这份工作棒极了，因为我们为任何人制造很酷的东西。但是像我们中的许多人一样，在我的个人学习中，我总是渴望做一些*不一样的*；在我们的团队和客户喜欢的技术范围之外工作。因此，我从“玩”编码中获得了很多乐趣，尤其是体验了 PHP、Python 和 C。

## 错过 JavaScript 火箭

虽然从 2011 年我的职业生涯开始以来，JavaScript 一直是前端开发的支柱，但当 Node/NPM 火箭起飞时，我冷静地看着。不在乎。让我们看看它去哪里。

[![Homer and Bart simpson miss a rocket takeoff](img/9fe670c682c04d6460f2abf033033f47.png)T2】](https://i.giphy.com/media/1SzFgSCH7LO9ntW9WT/giphy.gif)

现在，JavaScript 可能是所有语言中最令人兴奋和最繁荣的社区，这是一个有吸引力的前景(尽管它有好的一面也有坏的一面)！不过，似乎不值得费心去了解所有的工具，更不用说下载和设置它们了。另外，当你无知的时候，你不知道你不知道多少。好像很多。

## 查找故障

我第一次听说 Glitch 是在 Hyperdev 的一次设计回顾会上，但我并没有多想，直到 GitHub 的一些鼓舞人心的女士和我 twitter 上的其他人开始谈论它并使用它。

[![Glitch logo](img/bfecbc435d9fe7551b5f08c0fadc180c.png)T2】](https://glitch.com)

我发现看着你尊敬的人享受某件事是想要参与的最可靠的方式！我参与 GitHub 是因为看着那些人开心地互相编码，Glitch 就像历史在我身上重演。

我第一次真正使用 Glitch 是一个难得的机会，可以花一些时间和我的侄子在一起。我们决定一起学习一些代码。我想帮助他在互联网上制作自己的现实生活网站，格林奇突然出现在我的脑海里。我们做了一些东西，在我把它发到了推特上之后，我真的受到了反馈的鼓舞！

> ![Anil Dash profile image](img/85369669d144481c1c909e9a3a6f45fb.png)安尼尔达什@安尼尔达什![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)好爱这个故事！欢迎来到 Glitch 社区。:)[twitter.com/SteGriff/statu…](https://t.co/ocWFpAYvi1)22:15PM-2018 年 7 月 19 日Ste Griffiths@ Ste Griff花了一晚上的时间和我超级聪明的侄子在@Glitch 上教他怎么做网站！#StayingUpLate 检出 https://t.co/T1KTMfrWT2 https://t.co/r4cTEnmQyV[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1020069528481517572)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1020069528481517572)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1020069528481517572)12

这有点虚荣，但得到团队的认可让我想更多地使用 Glitch。我想这表明制造 Glitch 的人实际上关心人们使用它的乐趣。所以我又用了一些！

## 小故障和 JavaScript

[![Prepare for trouble, and make it double](img/379c86fee6e63869eb0e3edfa2c7a316.png)T2】](https://i.giphy.com/media/lDrg1c0wl2vy8/giphy.gif)

Glitch 低调地支持一系列运行时(T1)，但是它的第一语言是 JavaScript。所有基本的示例项目目前都是 JS，所以当我开始修改这些模板时，选择服务器端 JS 就成了乐趣和挑战的一部分。我最后一次尝试 Express 是在很多年前我还是一名初级开发人员的时候，当时我被吓到了。但是那时候:

A.我试图从零开始建立一切
B .我不容易获得高质量、简单的社区示例
C .我对路由、API 和像 NPM 这样的包管理器了解得更少

我把 C 放在最后，因为我认为它最不重要。这些模板现在呈现的方式，以及丰富的可工作的(可再混合！)Glitch 上的例子让更多的观众可以接触到它。

## 进入其中

我记性不好，所以我就做了这个小项目(当然是关于 Glitch！)调用 API 向我显示我在玩什么项目以及何时玩的图表:

[https://glitch.com/embed/#!/embed/glitch-timeline?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/glitch-timeline?previewSize=100&path=index.html)

当我构建更大的项目时，比如电子鸡日志和 BIOY，把问题分解成我需要学习的小部分是很有帮助的。为了制作电子鸡日志，我必须解决文件上传的问题(在两个不同的库中尝试过，只有一个库成功)。为了制作 BIOY，我不得不重新学习如何制作一个登录系统，这次使用的是 NPM 工具。我只和。Net 和 PHP 之前。甚至那个问题可以分解为[学习与 sqlite](https://github.com/mapbox/node-sqlite3/wiki/API#databaserunsql-param--callback) 接口，学习在 Node.js 中使用 [bcrypt(这里有一个](http://codetheory.in/using-the-node-js-bcrypt-module-to-hash-and-safely-store-passwords/)[更详细的指南我也用过](https://solidgeargroup.com/hashing-passwords-nodejs-mongodb-bcrypt))。

## 突刺的好处

因此，每个大项目都变成了一系列有趣的“尖峰”——每个功能的概念证明。这样的好处是，下次有事回去咨询。spike 是一个不言自明的例子，所以当我不可避免地忘记如何实现其中一个或多个目标时——过去的 Ste 就在那里，我可以从中受益！

## 结论

我显然不是最高级或最有经验的 Node.js 开发人员，它可能不会成为我的主要工具，但以一种授权(而不是令人沮丧)的方式学习新东西真的很有趣，我很高兴 Glitch 和更广泛的社区提供了这一点。

*   你有没有用 Glitch 或者类似 Codepen 这样的东西来学习一门新的语言/栈？
*   你觉得怎么样？
*   你如何看待用一系列“尖峰”组成一个更大的项目？

[![High five cat gif](img/5fd2b07e838bbeeef13ae1f8e852d785.png)T2】](https://i.giphy.com/media/vnnoqBjIrJ73y/giphy.gif)